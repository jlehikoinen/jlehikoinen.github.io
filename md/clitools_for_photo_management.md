Command Line Tools for Photo Management
=======================================

_Updated: 05.03.2017_

Some random commands and one-liners that I use when dealing with missing photo metadata, video orientation etc.

ExifTool
--------

[ExifTool Download Site](http://www.sno.phy.queensu.ca/~phil/exiftool/)

### Basic commands

[ExifTool Command-Line Examples](http://owl.phy.queensu.ca/~phil/exiftool/examples.html)

Copy all metadata from original file to target file:

```bash
$ exiftool -TagsFromFile sourceimage.jpg targetimage.jpg
```

Copy photo DateTimeOriginal value to FileModifyDate:

```bash
$ exiftool "-fileModifyDate<DateTimeOriginal" targetimage.jpg
```

### One-liners for batch editing

Copy DateTimeOriginal value to FileModifyDate:

```bash
$ for c in *.JPG; do exiftool "-fileModifyDate<DateTimeOriginal" "$c"; done
```

Copy all video metadata from original file (.MOV) to target file (.mp4):

```bash
$ for c in *.MOV; do exiftool -tagsFromFile "$c" "${c%.MOV}.mp4"; done
```

Case where photo is missing dateTimeOriginal tag.

First copy FileModifyDate value to DateTimeOriginal:

```bash
$ for c in *.JPG; do exiftool "-dateTimeOriginal<fileModifyDate" "$c"; done
```

Then copy original value back to FileModifyDate:

```bash
$ for c in *.JPG; do exiftool "-fileModifyDate<DateTimeOriginal" "$c"; done
```

HandBrakeCLI
------------

[HandBrakeCLI Download Site](https://handbrake.fr/downloads2.php)

[HandBrakeCLI Guide](https://trac.handbrake.fr/wiki/CLIGuide)

### Change video orientation

Find all portrait iPhone videos in current folder:

```bash
$ find . -name '*.mov' -exec bash -c '[ $(exiftool -s -s -s -Rotation "$1") == "90" ]' bash {} \; -print
```

Rotate video, portrait to landscape:

```bash
$ HandBrakeCLI -i source.mov -o target.mov --preset="High Profile" --rotate="angle=0:hflip=0"
```

---

Comments
--------

[gimmick:Disqus](trrt)
