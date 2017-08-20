Photo management
================

_Posted: 28.03.2015_

_Updated: 15.06.2017_

**Note! I have updated this post with the current [Dropbox Core API and Heroku solution](https://github.com/jlehikoinen/db-mover).
**

See the original Photo Management post [here](./photo_management_v1.md).

---

Here's my photo (and video) management setup. It's pretty much based on Federico Viticci's [photo management solution](http://www.macstories.net/tutorials/my-photo-management-workflow-early-2014/) except I have replaced Hazel with a cloud-based solution.

Nowadays most of the photos are taken with iPhones in our household and viewed later on with iPads. I have also Nikon 1 J1, but it's used quite rarely. I wanted to have one centralized storage for all photos and videos which is available from every Mac and iOS device. Here's how the photo and video management setup was built.

## Setup

**Components:**

- Heroku [web app](https://github.com/jlehikoinen/db-mover)
- Dropbox [Core API app](https://www.dropbox.com/developers/apps)
- [Dropbox Pro](https://www.dropbox.com/upgrade) for cloud storage

**Requirements:**

- Heroku account
- Dropbox installed on all devices
- Same Dropbox account used across all devices

### How it works?

In a nutshell:

1. Take a photo with iPhone
2. Open Dropbox app -> photo is automatically backed up (uploaded) to Dropbox
3. What happens on Dropbox and Heroku?
    3.1. Dropbox webhook monitors changes in Dropbox storage
    3.2. Dropbox webhook sends notification to Heroku web app
    3.3. Heroku web app receives notification and runs a script
    3.4. Script uses Dropbox Core API to move the photo to a correct folder based on file metadata
4. Open Dropbox or Unbound app on iPad and view the photo
5. Check the changes from Dropbox RSS event feed, from a log file in Dropbox or from Heroku log stream

### Folder tree example

The folder tree naming system is basically designed for [Unbound](https://itunes.apple.com/us/app/unbound-for-dropbox/id586086921?mt=8) app and easy sorting. The screenshot is in Finnish, so here's a crash course on Finnish language:

- kuvat = images/photos
- ja = and
- videot = videos

![Directory tree](https://dl.dropboxusercontent.com/u/3972607/trrt.me/photo_management/dirtree.png)

### iOS apps

I use two different iOS apps for different tasks with photos and videos.

- [Dropbox](https://itunes.apple.com/en/app/dropbox/id327630330?mt=8) for uploading and managing files and viewing logs
- [Unbound](https://itunes.apple.com/us/app/unbound-for-dropbox/id586086921?mt=8) for viewing media files

### Dropbox vs. Unbound

Here's a little Dropbox and Unbound comparison if you are not familiar with these apps. I use Dropbox for skimming through photos and videos and Unbound for viewing non-compressed videos and photo metadata.

| _Feature_ | Dropbox | Unbound |
| ------------ | -------- | -------- |
| **Content** | All Dropbox media | 1 Dropbox folder |
| **Caching** | Automatic | Automatic & manual per subfolder |
| **Video compression** | Yes | No |
| **Metadata viewer** | - | Photos only |
| **Price** | Free | 2,99 € (Not universal) |

### iOS Parental Control

If you don't want kids to access Dropbox, you can protect Dropbox with a separate passcode.

Dropbox on the other hand can be quite risky if you leave your device unsupervised. Dropbox has a Delete button visible when viewing a single photo or a video and there's currently no way of how to disable it. And it's no surprise that deleting files via Dropbox also deletes them from Dropbox.

Unbound has a Disable Delete functionality in the Settings > Viewing.

### Sharing

There are plenty of options how to share files with Dropbox or Unbound. Share a single file with a link or a complete folder etc.

If you have opted out using iCloud Photo Library or Photo Stream (like I have), you can still use iCloud sharing with friends or family members who have iOS devices. Here are my Photos & Camera settings:

![iOS Photos & Camera settings](https://dl.dropboxusercontent.com/u/3972607/trrt.me/photo_management/ios-photo-settings.png)

### Backups

Dropbox has it's own [version history system](https://www.dropbox.com/help/113), but always have a local backup solution in place as well.

If you plan to add lots of data to your Dropbox it might be wise to exclude Dropbox folder from your Time Machine backups on other Macs that use the same Dropbox account.

### Manual importing

This system works with manual importing as well. Just drop files to Camera Uploads folder on your Mac. It doesn't matter which Mac is used for this because when the files get synced to Dropbox cloud storage they are moved to correct location in the folder tree.

Only downside is that you need to select the files manually that you want to import. I use this [one-liner](./tricks.md#Finding_recently_created_or_edited_files) to find out last imported files.

## Summary

Compared to iCloud Photo Library and Photo Stream this has been a smooth run so far. No surprises, no duplicates or weird behavior.

If you don't like Dropbox, it can be easily replaced basically with any cloud storage provider or with your own NAS.

### Pros

- Dropbox has IMO the most robust synchronization system of the available cloud storage providers
- Dropbox integration in most common iOS apps and Dropbox action extension in iOS 8
- You can remove failed shots before photos are synced to the cloud
- File level management -> you see what's happening
- Possibility for endless customization :)

### Cons

- Find a fast Internet connection when uploading over 100GB of photos and videos
- Downloading/viewing larger HD videos from Dropbox might take some time
- Unbound is not universal app

---
