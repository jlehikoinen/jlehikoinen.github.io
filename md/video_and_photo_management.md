# Home video and photo management solution

_Posted: 11.06.2021_

---

(Add screenshots of 3 different views)

Here's my personal home video and photo management setup.

This Xth iteration of photo management solution. The current setup has been in use for about two years.

Here are two old iterations:

- [Version 1](../archive/md/photo_management.md)
- [Version 2](../archive/md/photo_management_v1.md)

## Problem: How to watch 4k home videos with Apple TV? Easy enough UI for kids?

- iCloud photos doesn't support 4k videos and is slow even with 1080p videos
- Most of the NAS media viewing solutions are too difficult for kids to use

## Components

- Python script which 
    - uses Dropbox API to move photos and videos from Camera Uploads folder to `year-month` folders
    - sends push notification using [Pushover](https://pushover.net)
- bash script that 
    - clones Dropbox photo and video folders to external disk using `rsync`
    - runs `create_thumbnails.zsh` script that creates thumbnails of videos
    - runs Swift command line tool `DboxMediaHelper` that creates a json file of photo and video metadata
- Apache web server running on Mac mini
- Apple TV app for displaying photos and videos

### create_thumbnails.zsh

- Enumerates files in video folders
- Creates directory tree if needed
- Uses `qlmanage` command line tool to create thumbnails of videos

### Swift command line tool

- Enumerates files in photo and video folders
- Gets photo and video metadata using Core Graphics APIs
- [LocationIQ](https://locationiq.com) API for reverse geocoding photo location
- Diffs contents of existing json file and enumerated metadata or creates a new json file

### Apple TV app

- uses TVMLKit framework
- downloads json blob from web server
- displays photos and videos using simple UI

### Excerpt of json metadata file

```
[
  {
    "year" : "2019",
    "month" : "2019-11-kuvat",
    "mediaItems" : [
      {
        "location" : "Petkeleentie, Lahti",
        "city" : "Lahti",
        "timeTaken" : "2019-11-14T16:19:52.000Z",
        "model" : "iPhone 11",
        "path" : "\/Kuvat ja videot\/Kuvat\/2019\/2019-11-kuvat\/2019-11-14 18.19.52.jpg",
        "type" : "photo",
        "coordinates" : {
          "lat" : 61.459541666666667,
          "lon" : 25.79663
        }
      },
      {
        "location" : "Petkeleentie, Lahti",
        "city" : "Lahti",
        "timeTaken" : "2019-11-10T10:27:58.000Z",
        "model" : "iPhone 8",
        "path" : "\/Kuvat ja videot\/Kuvat\/2019\/2019-11-kuvat\/2019-11-10 12.27.58.jpg",
        "type" : "photo",
        "coordinates" : {
          "lat" : 61.459861666666669,
          "lon" : 25.797280000000001
        }
      },
...
```

## Hardware

- Mac mini which runs the web server and all the previously mentioned tools ans scripts using LaunchDaemons

## Future iterations

- Port TVMLKit code to SwiftUI so it can be used with iPads etc.

---
