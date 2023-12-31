---
title: "Set up PhotoSync app to backup photos from your phone to an SMB share on your home server"
description: "Google Photos began limiting free storage back in 2021, limiting you to 15 GB of storage when uploading photos in their original size uncompressed. Rather than wait and see if I hit the cap, I decided to try replacing Google Photos with a self-hosted solution. Here's how I did it."
pubDate: "July 7, 2023"
tags:
  - Self-Hosting
  - PhotoSync
  - Android
  - Samba
---

## Table of Contents

1. [Caveats and Pre-Requisites](#pre)
2. [Configure PhotoSync](#config)
3. [Transferring photos to the Samba share](#transfer)
4. [References](#ref)

> **ⓘ Note**<br><br> Though the **PhotoSync** app is available on iPhone as well as Android, I have an Android phone and so **I have only done this on Android**. It's safe to assume configuring PhotoSync on iPhone to connect to your NAS will be similar, if not exactly the same. Feel free to leave a comment below confirming whether or not it works on iPhone!

<div id='pre'/>

## Caveats and Pre-Requisites

The method I describe below requires that your phone with the PhotoSync app be connected to the same network as the server that you will be transferring your photos to, or have access to the server via a VPN or other similar solution. Also, the server must already be configured with a Samba share. (<a href="/blog/setup-a-samba-share-on-linux-via-command-line" target="_blank">See here on how to set up a Samba share on Linux.</a>)

Finally, using the SMB option to transfer photos requires the NAS Add-On for PhotoSync, which costs $2.49 by itself. (You also have the option of spending a bit more on the Bundle Add-On, which comes with the NAS Add-On as well as the Auto-Transfer and Cloud Add-Ons, but those are not necessary for this.)

<div id='config'/>

## Configure PhotoSync

In the PhotoSync app, tap the **gear icon** at the bottom-right corner to enter the Settings.

<div class="two-img">
  <div>
    <a href="/img/blog/photosync1.jpg" target="_blank"><img src="/img/blog/photosync1.jpg" alt="Screenshot of Photosync settings"></a>
  </div>
  <div>
    <a href="/img/blog/photosync2.jpg" target="_blank"><img src="/img/blog/photosync2.jpg" alt="Screenshot of Photosync settings"></a>
  </div>
</div>

Under **Transfer Targets** tap on **Configure**. On the following page choose **SMB** from the list, then tap **Add New Configuration**.

<div class="two-img">
  <div>
    <a href="/img/blog/photosync3.jpg" target="_blank"><img src="/img/blog/photosync3.jpg" alt="Screenshot of Photosync SMB configuration"></a>
  </div>
  <div>
    <a href="/img/blog/photosync4.jpg" target="_blank"><img src="/img/blog/photosync4.jpg" alt="Screenshot of Photosync SMB configuration"></a>
  </div>
</div>

Enter the IP address of the server where the Samba share is, enter the login and password (assuming you have it set up that way), then tap the **magnifying glass button next to Directory**. The app should automatically show any Samba shares already set up on the server, tap on the one you want to use. Next tap on **Connect** at the top-right corner and if everything works correctly, you should be sent to the SMB target page.

Now under **FOLDER SETTINGS** tap on **Destination Folder** and pick a directory in the share, if you'd like, or just use the Share's root directory if you prefer, then tap **Select** on the top-right.

<div class="two-img">
  <div>
    <a href="/img/blog/photosync5.jpg" target="_blank"><img src="/img/blog/photosync5.jpg" alt="Screenshot of Photosync SMB account settings"></a>
  </div>
  <div>
    <a href="/img/blog/photosync6.jpg" target="_blank"><img src="/img/blog/photosync6.jpg" alt="Screenshot of Photosync SMB configuration"></a>
  </div>
</div>

The rest of the settings you can set to your liking. If you haven't gotten any errors, everything should be working as intended. Tap on **Done** to return to the app's main page.

<div id='transfer'/>

## Transferring photos to the Samba share

Back on the main page, tap the **red transfer icon at the top**, tap **All** (or you can tap individual photos and choose **Selected**), then tap **SMB**.

<div class="two-img">
  <div>
    <a href="/img/blog/photosync7.jpg" target="_blank"><img src="/img/blog/photosync7.jpg" alt="Screenshot of Photosync photo transfer interface"></a>
  </div>
  <div>
    <a href="/img/blog/photosync8.jpg" target="_blank"><img src="/img/blog/photosync8.jpg" alt="Screenshot of Photosync photo transfer interface"></a>
  </div>
</div>

Tap on the directory to transfer your photos into (keep in mind **Destination Folder** and **Create Sub-Directories** in the SMB Account setting from earlier) and tap Select, the photo transfer should begin.

<a href="/img/blog/photosync9.jpg" target="_blank"><img src="/img/blog/photosync9.jpg" alt="Screenshot of Photosync transfering photos"></a>

> **✔ Success!**<br><br> All done! Now you can manually sync new photos from your phone to your NAS at any time by opening the app and repeating the last set of instructions above. _If you want background auto-transfers, you'll need the Auto-Transfer Add-On._ 

<div id='ref'/>

## References

- <a href="https://www.photosync-app.com/home" target="_blank">PhotoSync Website</a>
- <a href="/blog/setup-a-samba-share-on-linux-via-command-line">My blog post on how to set up a Samba Share
