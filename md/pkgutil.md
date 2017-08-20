pkgutil
=======

Note! Testing Markdown formatting..

Case example: Adobe Flash Player
--------------------------------

Find out package identifier:

`$ pkgutil --pkgs | grep adobe`

```
...
com.adobe.pkg.FlashPlayer
...
```

Find out package information:

`$ pkgutil --pkg-info com.adobe.pkg.FlashPlayer`

```
package-id: com.adobe.pkg.FlashPlayer
version: 12.0.0.77
volume: /
location:
install-time: 1394606790
```

List all the files Flash Player Installer installed to your system:

`$ pkgutil --files com.adobe.pkg.FlashPlayer`

```
Applications
Applications/Utilities
Applications/Utilities/Adobe Flash Player Install Manager.app
Applications/Utilities/Adobe Flash Player Install Manager.app/Contents
...
```

Discard all Adobe Flash Player recipe data:

`$ sudo pkgutil --forget com.adobe.pkg.FlashPlayer`

---
