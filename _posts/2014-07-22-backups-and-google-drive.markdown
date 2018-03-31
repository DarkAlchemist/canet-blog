---
title: Backups and Google Drive
date: '2014-07-22 15:17:05'
tags:
- linux
- backups
- google
---

Having recently migrated from a dedicated server to VPS for hosting a mirrored solution of some of my home setup (Dovecot/Postfix), I had a bit of an issue in working out what to do about backups of large data sets such as my photos and music collection. One of the major issues I came across was that large amounts of online storage space tends to be quite expensive when attached to VPS or when provided by a decent dedicated server. 

## Google Drive
It was at this point when Google Drive had recently announced 1TB of storage space for $9.99 a month making it a very cheap storage solution so I decided to do a bit more research into it to find out the benefits and drawbacks.

### Benefits
* Cheap at $9.99 per TB (with UK tax at 20% and the conversion rate, it is about £7 a month)
* Quick to access with apps for Windows, OSX, Android and iOS and a brilliant webapp
* Has a couple of music player addons to stream music from your browser as well as dedicated Android apps for music streaming with Drive as a storage backend
* Should be as redundant as Google’s storage for other services with nearly no downtime

### Drawbacks
* Mainly API based access for 3rd parties
* No Linux server clients for syncing (which is a bit of a problem given my Gentoo-based storage server)
* Music playback apps leave something to be desired regarding interface and usability

## gdrsync
This looked like a good option for me except for the lack of Linux support so I did a bit of digging around and came across a tool called **gdrsync**. This attempts to emulate the **rsync** tool with the explicit intention of use against Google Drive. The only problem with the original version of this was that it didn’t support downloading the data back down. Luckily I found a fork [here](https://github.com/quisar/gdrsync.py/tree/with-download-2) which does support it. Using the following with it preconfigured in the instructions in the README, I can sync any set of files to a given path in Google Drive from my Linux server.

	gdrsync -rdvs /path/to/files/ gdrive:/path/to/files/

It’s worth noting you need to set up a development account and acquire a key and ID to go with this from Google but it’s a fairly straightforward process and doesn’t cost anything. It will work with the default configuration from the original developer but if more and more people are using, it will start running into issues with API calls so I suggest getting your own set up.