---
title: Dovecot 'dsync' Replication Over TCP
date: '2014-05-31 12:43:36'
---

I've previously written about **dsync** as a tool provided by **dovecot** to migrate mailbox formats but it's also a tool that works over SSH and TCP for synchronizing mailboxes across two instances of **dovecot**.

**dsync** is primarily worth using because it uses **dovecot**'s log files to replicate as mail is received so filesystem corruption isn't replicated and it also avoids having to script and control synchronization between the two instances. Because it uses the log files, it can also run master-master and whilst the official documentation recommends a user always goes to the same host, it also states that the worse case scenario is a user has to redownload mails again. **dsync** will also sync your **sieve** configurations if you have **sieve** set up!

It is worth noting that this is a bit of a pain to set up, so be prepared! This guide was also written based on the **Gentoo** installation configurations.

#### Dovecot Services Used
* **doveadm** - Front facing TCP listening service providing access to **aggregator** and **replicator** I believe
*  **aggregator** - I believe this is used to provided notifications to the **replicator** process when there's changes to synchronize
* **replicator** - Triggers/handles synchronization

#### Pre-requisites
* `doveadm user '*'` should return a list of all the users **dovecot** hosts. I found this needed to be a complete list in email format for it to replicate properly (this is possibly in part due to me organizing my mail folders with folders for domain followed by folders for users underneath)
* **Optional** - SSL Configured as per the [Dovecot Documentation on SSL](http://wiki2.dovecot.org/SSL/DovecotConfiguration). This is only required if you wish to use synchronization over SSL (and using **Gentoo**, you need to have **dovecot** compiled with `USE="ssl"`.
* **Optional (probably...)** Mailbox format in **mdbox** or **sdbox**. I'm not sure where I read this but with a large amount of mail, the **maildir** format is expensive to sync in comparison and may have more issues. If I find the documentation on this, I'll add it in.

#### Configuration (On both hosts)
1. `/etc/dovecot/conf.d/10-mail.conf`

		# Enable globally the notify and replication plugins
    	# This will then apply to all protocols dovecot supports
		mail_plugins = notify replication
        
* `/etc/dovecot/conf.d/30-dsync.conf` - You'll need to create this manually

		# This sets globally the port needed to connect
        # to the doveadm service
        doveadm_port = 1234
        
        # Set the password globally to use for connecting to 
        # and requiring for the doveadm service 
        doveadm_password = password
        
        # Set the directory for validating certificates
        # provided when running with SSL enabled
        # Should be the standard OpenSSL certificate directory by default
        ssl_client_ca_dir = /etc/ssl/certs
        
        # Configure the doveadm service to listen
        service doveadm {
        	inet_listener {
            	# port to listen on
            	port = 1234
                # enable SSL
                ssl = yes
            }
        }
        
        # Configure the aggregator service for notifications
        service aggregator {
        	fifo_listener replication-notify-fifo {
            	# Your mail user that's managing files generally is used here
            	user = mail
            }
            unix_listener replication-notify {
            	user = mail
            }
        }
        
        # Configure the replicator service
        service replicator {
        	process_min_avail = 1
            unix_listener replicator-doveadm {
            	mode = 0666
            }
        }
        
        # Configure target hosts for replication
        # tcps can be tcp if you don't want to connect with SSL
        # :port can be omitted if it's the default set globally for doveadm
        plugin {
        	mail_replica = tcps:fqdn:port
        }
* Restart **dovecot**
        
#### Notes on SSL
* By default, **doveadm** will use the certificates set globally for `ssl_key` and `ssl_cert` if you tell it to listen on SSL. It does not support SNI like the **imap** and **pop3** listeners nor does **dovecot** when it attempts to connect to the other instance.
* Unlike the **imap** and **pop3** listeners, **doveadm** does not supply all certificates in the chain, even if they are in the certificate file. This means that verifying is left down to the certificates in `/etc/ssl/certs`. I found that the intermediate certificate for StartCom was missing from there and needed adding. After adding, you need to run `c_rehash` to update hashes in the directory that are used by OpenSSL for lookup.

#### Useful Commands
* `doveadm replicator replicate <email>`  - Replicate a given email account manually
* `doveadm replicator replicate -f <email>`  - Replicate a given email account manually **IN FULL**
* `doveadm replicator status <email>`  - Check replication status. Also works without the email parameter