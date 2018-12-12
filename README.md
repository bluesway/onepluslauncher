![OnePlusLauncher logo](https://lh3.googleusercontent.com/b-40YX8xbQsPxOVsPWchsaNRv5cJBqvF0jVGNHBsllUTURVQ8EGmCVsMy1RadIpXuekg=s240)

Instruction to Engage with OnePlus Launcher Shelf
=============================
![MarkDown logo](https://upload.wikimedia.org/wikipedia/commons/thumb/4/48/Markdown-mark.svg/64px-Markdown-mark.svg.png)
###### IMPORTANT: This document is written with former MarkDown syntax, reading with a MarkDown viewer is best recommended

# Manage your App Widget on Shelf
Guidance
--------
### Grant permission to access Shelf
```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapplication" >
    <uses-permission android:name="com.oneplus.launcher.permission.NOTE" />
    <uses-permission android:name="com.oneplus.launcher.permission.READ_SETTINGS" />

    <application
    <!-- other contents -->
    </application>
</manifest>
```
## Limitation

# Implement *General Card* with Shelf SDK
Normal Card
-----------
### Description
### Getting started
#### Write a CardProvider
#### Update the card with specific style on callback received

VIP Card
--------
### Description
### Getting started
#### Write a CardProvider
#### Create the card with specific style
#### Post the card onto Shelf
