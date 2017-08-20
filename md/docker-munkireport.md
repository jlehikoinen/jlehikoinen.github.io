MunkiReport & Docker testing
============================

_Posted: 16.06.2015_

_Edited: 30.06.2015 ("Test custom configurations" part is now little bit simpler)_


---

I wanted to test [MunkiReport](https://github.com/munkireport/munkireport-php) and MySQL in non-production environment and didn't want to install MySQL on my host machine. I've tested [Docker](https://www.docker.com) couple of times before but never really "needed" it for Mac admin related tasks, except now. Here are few (not very verbose) notes about how MunkiReport test environment was built using Docker.

**Requirements**

- [Git](https://git-scm.com)
- [boot2docker](http://boot2docker.io) (because Mac)
- [Docker](https://www.docker.com)
- [Sequel Pro](http://www.sequelpro.com) (optional)

**Docker images @ Docker Hub**

* [MySQL](https://registry.hub.docker.com/_/mysql/)
* [MunkiReport](https://registry.hub.docker.com/u/hunty1/munkireport-docker/) (Thanks to Calum Hunter!)

Preparations
------------

Launch Terminal and run boot2docker VM:

```bash
$ boot2docker up
```

Set environment variables:

```bash
$ eval "$(boot2docker shellinit)"
```


Get images
----------

Get the latest MySQL Docker image:

```bash
$ docker pull mysql
```

Get the latest MunkiReport Docker image:

```bash
$ docker pull hunty1/munkireport-docker
```

Run containers
--------------

Run MySQL container (**db**) & create a database for MunkiReport:

```bash
$ docker run --name db -p 3306:3306 -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=munkireport -e MYSQL_USER=admin -e MYSQL_PASSWORD=admin -d mysql
```

Run MunkiReport (**mr**) container and connect it to **db** container. Change environment variables (if needed):

```bash
$ docker run -p 80:80 --name mr -e DB_NAME=munkireport -e DB_USER=admin -e DB_PASS=admin -e DB_SERVER=$(boot2docker ip) -e MR_SITENAME="docker.local" --link db:mysql -d hunty1/munkireport-docker
```

Check that the both containers are running properly:

```bash
$ docker ps
```

Output example:

```
CONTAINER ID        IMAGE                              COMMAND                CREATED             STATUS              PORTS                         NAMES
6fd969c1c6ad        hunty1/munkireport-docker:latest   "/start.sh"            10 seconds ago      Up 7 seconds        0.0.0.0:80->80/tcp, 443/tcp   mr
c22da21049c0        mysql:latest                       "/entrypoint.sh mysq   31 seconds ago      Up 28 seconds       0.0.0.0:3306->3306/tcp        db
```

MunkiReport GUI
---------------

Get boot2docker IP address:

```bash
$ boot2docker ip
```

_This IP address will be used in the rest of the examples._

```
192.168.59.103
```

Go to <http://192.168.59.103> and login with these credentials:

* Username: root

* Password: root

If everything went ok, you should see MunkiReport GUI with zero machines.

Test MunkiReport with a client
------------------------------

Note! You need [Munki](https://github.com/munki/munki) and [MunkiReport](https://github.com/munkireport/munkireport-php) installed on your host computer for this part.

Change MunkiReport `BaseUrl` on the host computer (notice the trailing slash!):

```bash
$ sudo defaults write /Library/Preferences/MunkiReport.plist BaseUrl http://$(boot2docker ip)/
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
$ cp path/to/<my-db>.sql ~/Desktop/
```

Run separate MySQL client container and destroy it after database import:

```bash
$ docker run -it --link=db:mysql -v ~/Desktop:/tmp/ --rm mysql sh -c 'exec mysql -h192.168.59.103 -P3306 -uroot -proot munkireport < /tmp/<my-db>.sql'
```

Ignore the warning message:

```
Warning: Using a password on the command line interface can be insecure.
```

Connect to the database using Sequel Pro:

* Host: 192.168.59.103
* Username: root
* Password: root
* Database: munkireport

Or connect directly to the MySQL container and use `mysql` command line tool.

```bash
$ docker exec -it db bash
```

```bash
mysql
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
$ docker run -p 80:80 --name mr --link db:mysql -v ~/Desktop/munkireport:/www/munkireport -e DB_NAME=munkireport -e DB_USER=admin -e DB_PASS=admin -e DB_SERVER=$(boot2docker ip) -e MR_SITENAME="docker.local" -d hunty1/munkireport-docker
```

Edit files in `~/Desktop/munkireport` folder:

* config.php
* widgets
* modules
* etc.

Refresh MunkiReport web GUI and login again: <http://192.168.59.103/>

After testing
-------------

Stop MunkiReport and MySQL containers:

```bash
$ docker stop mr db
```

Delete all Docker containers:

```bash
$ docker rm $(docker ps -a -q)
```

Shutdown boot2docker VM:

```bash
$ boot2docker down
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
