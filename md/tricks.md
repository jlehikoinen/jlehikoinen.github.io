Command line stash
==================

_Updated: 02.10.2017_

Tricks & one-liners
-------------------

### Send mail

Send mail from command line (Postfix configured):

`$ date | mail -s "Postfix test" my.email@domain.com`

### More about find

List directories and files that don't have default permissions and ignore `.git` subfolder:

`$ find . ! -path "./.git/*" -type d ! -perm 644 -o -type f ! -perm 755`

### Resize live partition

Get disk identifier:

`$ diskutil list`

Output example:

```
...
2:     Apple_HFS Macintosh HD     30.0 GB   disk1s2
...
```

Resize partition to 50 GB:

_APFS_

`$ diskutil apfs resizeContainer disk1s2 50g`

_HFS+_

`$ diskutil resizeVolume disk1s2 50G`

Resize partition to it's maximum size:

_HFS+_

 `$ diskutil resizeVolume disk1s2 R`

### Few `screen` commands.

Start new screen session:

`screen`

Detach from the current session:

`ctrl + a` > `d`

Attach to a previous session:

`screen -r`

List sessions:

`screen -ls`

Quit session:

`ctrl + a` > `:quit`


### Modify date formats

Source file contains multiple dates as one date / line.

Old format: `20-08-2015`

New format: `20150820`

Convert all dates to new format:

```bash
k=0; while read line; do let k+=1; echo $line | awk -v FS=- -v OFS= '{print $3,$2,$1}'; done < old_dates.txt > new_dates.txt
```

---

### Vim tricks

Replace tabs with spaces:

```bash
:set list
:set tabstop=4 shiftwidth=4 expandtab
:retab
```

Replace Windows line feeds with UNIX line feeds:

```bash
:%s/^M/\r/g
```

---

### Finding recently created or edited files

Find last 5 photos & videos imported from Nikon (DSC_XXXX.JPG|MOV):

```bash
$ find . -type f -name "DSC_*" -print0 | xargs -0 stat -f "%m %Sm %SN" -t "%d.%m.%Y %H:%M" | sort -nr | cut -d " " -f 2- | head -5
```

Example output:
```
24.02.2015 19:27 ./Kuvat ja videot/Kuvat/2015/2015-02-kuvat/DSC_3143.JPG
15.02.2015 09:10 ./Kuvat ja videot/Kuvat/2015/2015-02-kuvat/DSC_3140.JPG
15.02.2015 09:10 ./Kuvat ja videot/Kuvat/2015/2015-02-kuvat/DSC_3139.JPG
09.01.2015 17:49 ./Kuvat ja videot/Kuvat/2015/2015-01-kuvat/DSC_3138.JPG
09.01.2015 17:48 ./Kuvat ja videot/Videot/2015/2015-01-videot/DSC_3137.MOV
```

List of modified files from the past 7 days (excluding Dropbox cache + .DS_Store and Icon? files):

```bash
$ find . -type f ! -path "./.dropbox.cache/*" ! -name ".DS_Store" ! -name "Icon?" -mtime -7 -print0 | xargs -0 stat -f "%m %Sm %SN" -t "%d.%m.%Y %H:%M" | sort -nr | cut -d " " -f 2-
```

Sample output:

```
...
15.03.2015 12:14 ./path/to/1.jpg
15.03.2015 12:13 ./path/to/2.jpg
15.03.2015 12:11 ./path/to/1.txt
15.03.2015 12:10 ./path/to/2.txt
...
```

---

### Renaming batch of files using for loop & shell expansion

Change file extension from `.log` to `.txt`:

```bash
$ for file in *.log; do mv -i "$file" "${file%.log}.txt"; done
```

Replace `Photo` with `Pic`:

```bash
$ for file in Photo*; do mv -i "$file" "${file/#Photo/Pic}"; done
```

Replace `Photo*.png` with `2014_[digit].png`:

```bash
$ d=0; for file in Photo*; do let d+=1; mv -i "$file" 2014_$d.png; done
```

Same as previous but with one leading zero:

```bash
$ d=0; for file in Photo*; do let d+=1; mv -i "$file" 2014_$(printf "%02d\n" $d).png; done
```

Remove 12 characters before file extension from a file name:

```bash
for file in *.mp4; do mv -i "$file" "${file%%????????????.mp4}.mp4"; done
```

Example:

```
Some youtube video (episode 1)-RiTAdApC5-Y.mp4 => Some youtube video (episode 1).mp4
```

---

### SSH

Generate RSA key pair for SSH:

```bash
$ ssh-keygen -t rsa -b 4096 -f path/to/<some>.key -C "<description>"
```

---

List top 10 user names used in SSH break-in attempts:

_macOS 10.12+_

```bash
$ log show --info --last 24h | grep -o "Invalid user.*" | awk '{print $3}' | sort | uniq -c | sort -nr | head -10
```

_macOS 10.11 and earlier_

```bash
$ grep -o "Invalid user.*" /var/log/system.log | awk '{print $3}' | sort | uniq -c | sort -nr | head -10
```

List successful SSH logins using public key method:

_macOS 10.12+_

```bash
$ log show --info --last 24h | grep "Accepted publickey"
```

_macOS 10.11 and earlier_

```bash
$ grep "Accepted publickey" /var/log/system.log
```

---

### Apple Configuration Profile mangling

Strip code signing information from profile:

```bash
$ security cms -D -i Signed.mobileconfig -o Unsigned.mobileconfig
```

Ignore warning message:

```
security: SecPolicySetValue: One or more parameters passed to a function were not valid.
```

Reindent a xml/plist/mobileconfig file:

```bash
$ xmllint --format original.mobileconfig > modified.mobileconfig
```

Combined:

```bash
$ security cms -D -i Signed.mobileconfig | xmllint --format - > Unsigned.mobileconfig
```

---

### Misc

Current SSID name:

```bash
$ /System/Library/PrivateFrameworks/Apple80211.framework/Versions/Current/Resources/airport -I | grep -w "SSID" | awk -F": " '{print $2}'
```

---

Get weekday for certain date:

```bash
$ date -jf "%d.%m.%Y" "30.07.2010" +%A
```

---

Mount encrypted (sparse disk) image:

```bash
$ hdiutil attach -stdinpass /path/to/image.dmg
```

---

Search directories recursively for files matching 'string':

```bash
$ grep -r -l 'string' /path/to/dir
```

---

Show original file location if symlink:

```bash
$ readlink /tmp
```

---

Use multicast address to find out all multicast capable hosts:

```bash
$ ping 224.0.0.1
```

---

List unique IP addresses in Apache access log:

```bash
$ cat /var/log/apache2/access_log | awk '{print $2}' | sort | uniq -c | sort -nr
```

Number of unique IP addresses in Apache access log:

```bash
$ cat /var/log/apache2/access_log | awk '{print $2}' | sort | uniq -c | wc -l
```

---

List macOS installer package bill of materials:

```bash
$ lsbom -p MUGsf /path/to/Archive.bom
```

If flat package, first expand the package contents to a temp folder:

```bash
$ pkgutil --expand /path/to/installer.pkg ~/Desktop/temp_out
```

---

Show used disk space on boot volume (total):

```bash
$ df -h / | awk 'NR > 1 {print $3}'
```

---
