---
title: Dovecot 'mdbox' Format Maintenance
date: '2014-05-30 17:32:28'
---

I ran into a wierd situation the other day when setting up Dovecot `dsync` replication over TCP where the new host I was syncing too, had finished syncing but the directory size differed between both hosts. After a bit of investigation, I found this was a quirk of sorts in how the `mdbox` format works.

`mdbox` stores messages even after they are completely deleted and `expunged`. This is done by decremented a variable called `refcount` on the message and when there is finally no more references for it, the value should be set to `0`. Even after this though, the message is still stored in the file. When syncing however over `dsync`, these messages are not replicated as it is specific to the file format.

Apparently, during quiet periods of the day, it is recommended to schedule a maintenance command via `cron` for purging these messages completely. This can be done as follows:

	# To purge a single user's mails where refcount=0
    doveadm purge -u <user>
    
    # To purge all users' mails where refcount=0
    doveadm purge -A
    
For the latter to succeed, `doveadm user '*'` must successfully return all the correct usernames for deletion. This of course is now running on both hosts as a nightly `cron` job to keep things tidy.