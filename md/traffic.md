Finnish Traffic Notifications
=============================

_Posted: 08.02.2016_

There's a pretty useful traffic service called [V-Traffic](http://www.v-traffic.com/?c=fi&uil=fi) available from where you can get live Nordic traffic information. They even have their own [iOS app](https://itunes.apple.com/fi/app/vtrafficnordic/id567949372?mt=8), but it's lacking one little feature, notifications from the places you'd like to receive traffic reports.

No worries, you can get live traffic reports with your own custom search terms automatically to your iPhone using V-Traffic [Twitter](https://twitter.com/vtrafficNordic) feed and [IFTTT](https://ifttt.com) recipe.

## Setup

1. Register [Twitter](https://twitter.com) account and [IFTTT](https://ifttt.com) account (if you don't have those yet)

2. Install [IFTTT iOS app](https://itunes.apple.com/us/app/if-by-ifttt/id660944635?mt=8)

3. Add "if-new-tweet-send-notification" [IFTTT recipe](https://ifttt.com/recipes/382316-nordic-traffic-notifications) as a template

4. Connect your IFTTT account to [Twitter channel](https://ifttt.com/twitter) (if needed)

### Edit IFTTT recipe

Note! The screenshots below are from IFTTT website, on iOS app the Recipe editor looks little bit different.

Edit Recipe Title to match your search parameters:

![IFTTT](https://dl.dropboxusercontent.com/u/3972607/trrt.me/traffic/ifttt1.png)

Replace `"Tie 4"` placeholder search parameter with your own search keyword(s).

![IFTTT](https://dl.dropboxusercontent.com/u/3972607/trrt.me/traffic/ifttt2.png)

Click the **Update** button to save your changes.

### Twitter advanced search

Check out this [article](https://support.twitter.com/articles/71577?lang=en) for more details on Twitter search parameters.

### iOS Notifications Settings

You can adjust how notifications are displayed on your iPhone in Settings > Notifications > IF:

![iOS Notifications Settings](https://dl.dropboxusercontent.com/u/3972607/trrt.me/traffic/ios-notification-settings.png)

Notification example:

![iOS Notification](https://dl.dropboxusercontent.com/u/3972607/trrt.me/traffic/ios-notification.png)

### Considerations

For testing purposes it might be a good idea to add more popular search term like "Kehä I" or "Helsinki" to the recipe to receive test notifications.

---
