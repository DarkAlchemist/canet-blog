---
title: Migrating Mailbox Formats With Dovecot
date: '2013-05-21 19:00:59'
tags:
- linux
- dovecot
- imap
---

A bit of background is required first to put this post into context. I currently run Dovecot for IMAP to host my own domains. Having recently acquired a secondary hosting location, I was looking into mirroring potential to sync up the two sites. Fortunately, Dovecot has a useful tool known as 'dsync' for this purpose which syncs up mail between two Dovecot instances as well as a few other helpful things. The only downside is that before Dovecot 2.2, it supposedly has a few issues (and Dovecot 2.2 is not marked as stable on Gentoo yet).

However, even on earlier versions of Dovecot, it is not recommended to use **Maildir** as a storage format and it is suggested to use **mdbox** instead. And thus, I decided to start off by converting from **Maildir** to **mdbox** for my storage format.

Instructions are as follows:

* Stop Dovecot (and be aware, it won't be available for a while)

* For the purposes of this, the root directory I use for storing mail is `/var/vmail/`

* Backup your mail directory (just in case although you should already be making backups regularly!)

* In `/etc/dovecot/conf.d/10-mail.conf` change 

		mail_location = maildir:/var/vmail/path/to/maildir
	to 
    
    	mail_location = mdbox:/var/vmail/path/to/mdbox

* In `/etc/dovecot/conf.d/15-mailboxes.conf`, in the default namespace, set

		seperator = .

	**NOTE:** You only need to do this if it is not already set!

* Next, start Dovecot back up and set up some iptables rules to block incoming connections whilst you migrate mailboxes.

* For each user run:

		dsync -vu <EMAIL> mirror maildir:<MAILDIR_DIRECTORY>
        
	Where `<EMAIL>` is the primary email address of the user to migrate and `<MAILDIR_DIRECTORY>` is the original directory used to store messages in the 'Maildir' format for that user. This will sync everything from the 'Maildir' directory to the 'mdbox' one configured above.

	If there are a lot of users to migrate, it is possible to set per-user mail directories in MySQL and change the directory over once each one is synced to the new format. If you're using MySQL already as a backend for Dovecot, it should be fairly straightforward to edit the drivers to get the information. Likewise, it's theoretically possible you could do this using LDAP as a user backend too. 