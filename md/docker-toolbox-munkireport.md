Docker & MunkiReport testing
============================

_Posted: 18.09.2015_

**Note! I have updated this post with Docker Toolbox and some other modifications.
**

See the original Docker & MunkiReport post [here](./docker-munkireport.md).

---

I wanted to test [MunkiReport](https://github.com/munkireport/munkireport-php) and MySQL in non-production environment and didn't want to install MySQL on my host machine. I've tested [Docker](https://www.docker.com) couple of times before but never really "needed" it for Mac admin related tasks, except now. Here are few (not very verbose) notes about how MunkiReport test environment was built using Docker.

**Requirements**

- [Git](https://git-scm.com)
- [Docker Toolbox](https://www.docker.com/toolbox)
- [Sequel Pro](http://www.sequelpro.com) (optional)

**Docker images @ Docker Hub**

* [Ubuntu](https://hub.docker.com/_/ubuntu/) (for data container)
* [MySQL](https://registry.hub.docker.com/_/mysql/)
* [MunkiReport](https://registry.hub.docker.com/u/hunty1/munkireport-docker/) (Thanks to Calum Hunter!)

Preparations
------------

Launch Terminal and start default Docker Machine:

```bash
$ docker-machine start default
```

Set environment variables:

```bash
$ eval "$(docker-machine env default)"
```

Run containers
--------------

Create data container **db_data** based on Ubuntu image:

```bash
docker run -d -v /var/lib/mysql --name db_data ubuntu
```

Run MySQL container **db_app**, connect it to the data container **db_data** & create a database for MunkiReport:

```bash
$ docker run -d --name db_app --volumes-from db_data -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=munkireport -e MYSQL_USER=admin -e MYSQL_PASSWORD=admin mysql
```

Run MunkiReport **mr** container and connect it to **db_app** container. Change environment variables (if needed):

```bash
$ docker run -d -p 80:80 --name mr --link db_app:mysql -e DB_NAME=munkireport -e DB_USER=admin -e DB_PASS=admin -e DB_SERVER=db_app -e MR_SITENAME="Local tests" hunty1/munkireport-docker
```

Check that the both app containers are running and the data container is visible:

```bash
$ docker ps -a
```

Output example:

```
CONTAINER ID        IMAGE                       COMMAND                  CREATED             STATUS                      PORTS                         NAMES
f9fa44a71498        hunty1/munkireport-docker   "/start.sh"              5 seconds ago       Up 4 seconds                0.0.0.0:80->80/tcp, 443/tcp   mr
8459a79a15ab        mysql                       "/entrypoint.sh mysql"   18 seconds ago      Up 17 seconds               0.0.0.0:3306->3306/tcp        db_app
b9ec4a17adb8        ubuntu                      "/bin/bash"              26 seconds ago      Exited (0) 25 seconds ago                                 db_data
```

MunkiReport GUI
---------------

Get Docker machine IP address:

```bash
$ docker-machine ip default
```

_This IP address will be used in the rest of the examples._

```
192.168.99.100
```

Go to <http://192.168.99.100> and login with these credentials:

* Username: root

* Password: root

If everything went ok, you should see MunkiReport GUI with zero machines.

Test MunkiReport with a client
------------------------------

Note! You need [Munki](https://github.com/munki/munki) and [MunkiReport](https://github.com/munkireport/munkireport-php) installed on your host computer for this part.

Change MunkiReport `BaseUrl` on the host computer (notice the trailing slash!):

```bash
$ sudo defaults write /Library/Preferences/MunkiReport.plist BaseUrl http://$(docker-machine ip default)/
```

Run managedsoftwareupdate:

```bash
$ sudo /usr/local/munki/managedsoftwareupdate
```

Refresh MunkiReport web GUI and check your host computer info.

Database import
---------------

It's nice to have MunkiReport up and running but it's quite useless if there's no data to view. Run mysqldump on your production server and copy the MySQL database file to your Desktop:

```bash
$ cp path/to/my-munkireport-db.sql ~/Desktop/
```

Run separate MySQL client container and destroy it after database import. Replace `my-munkireport-db.sql` with your db file name:

```bash
$ docker run -it --rm mysql --link=db_app:mysql -v ~/Desktop:/tmp/ sh -c 'exec mysql -h192.168.99.100 -P3306 -uroot -proot munkireport < /tmp/my-munkireport-db.sql'
```

You can ignore the warning message because we're only testing:

```
Warning: Using a password on the command line interface can be insecure.
```

Connect to the database using Sequel Pro:

* Host: 192.168.99.100
* Username: root
* Password: root
* Database: munkireport

Or connect directly to the MySQL database with command line client:

```bash
docker exec -it db_app sh -c 'exec mysql -h192.168.99.100 -P3306 -uroot -proot munkireport'
```

Test custom configurations
--------------------------

Copy the `munkireport` folder from MunkiReport container to your Desktop:

```bash
$ docker cp mr:/www/munkireport/ ~/Desktop/
```

Stop & delete current MunkiReport container:

```bash
$ docker stop mr
```

```bash
$ docker rm -f mr
```

Run MunkiReport container again and mount host volume `~/Desktop/munkireport` as a data volume. Change environment variables once again (if needed):

```bash
$ docker run -d -p 80:80 --name mr --link db_app:mysql -v ~/Desktop/munkireport:/www/munkireport -e DB_NAME=munkireport -e DB_USER=admin -e DB_PASS=admin -e DB_SERVER=db_app -e MR_SITENAME="Local tests" hunty1/munkireport-docker
```

Edit files in `~/Desktop/munkireport` folder:

* config.php
* widgets
* modules
* etc.

Refresh MunkiReport web GUI and login again: <http://192.168.99.100/>

After testing
-------------

Stop all containers:

```bash
$ docker stop $(docker ps -aq)
```

Delete all Docker containers:

```bash
$ docker rm -f $(docker ps -aq)
```

Shutdown default Docker Machine:

```bash
$ docker-machine stop default
```

Change MunkiReport `BaseUrl` back to the original one:

```bash
$ sudo defaults write /Library/Preferences/MunkiReport.plist BaseUrl http://your_munki_repo/
```

Additional information
----------------------

Check out [Calum Hunter's GitHub page](https://github.com/hunty1/munkireport-docker) for more info if you want to tinker with MunkiReport container settings.

---

Comments
--------

[gimmick:Disqus](trrt)
