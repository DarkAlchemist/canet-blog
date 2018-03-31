---
title: OpenID and LDAP
date: '2013-06-30 12:42:54'
tags:
- linux
---

As part of my quest to have all webapps use Single-Sign-On of some form, I found that Zenphoto lacked decent LDAP support so I search for a different alternative. I found that Zenphoto did however support OpenID and found this useful plugin for . There are however a few issues with getting this working with PHP 5.4 and nginx.

The following is the excerpt I used for my nginx configuration to allow OpenID-LDAP to work correctly as it only supplies Apache configurations by default.

	location ~* .php$ {
    	try_files $uri = 404;
        fastcgi_pass 127.0.0.1:9000;
        include fastcgi.conf;
	}
	rewrite ^/([A-Za-z0-9]+)$ /index.php?user=$1 break;</pre>

`engine.php` also has problems with PHP 5.4 in that `session_is_registered()` is deprecated as a PHP function so we have to make do by replacing it with the following for every usage that appears.

	isset( $_SESSION['variable_name'] )

As for the configuration, this is done in `ldap.php` and below is an example of how to configure `$GLOBALS['ldap'] = array` which is used for the LDAP details.

		# Connection settings, primary and fallback servers
        'primary'               => 'ldap.example.com',
        'fallback'              => 'ldap2.example.com',
        'protocol'              => 3,
        
        # AD specific, both false as we are using OpenLDAP
        'isad'                  => false,
        'lookupcn'              => false, 
        
        # Binding account, set to blank if you don't need to bind for LDAP read access
        'binddn'                => '',
        'password'              => '',
        # autodn set to false broke for me
        'autodn'                => true,
        'testdn'                => 'uid=%s,ou=Users,dc=example,dc=com',
        # Searching data, you'll need to specify your OU
        'searchdn'              => 'ou=Users,dc=example,dc=com',
        # I want to log in with uid and password so I filter by uid
        'filter'                => '(uid=%s)',

        # SREG names matching to LDAP attribute names
        'nickname'              => 'uid',
        'email'                 => 'mail',
        # givenname is givenName in the default configuration 
        # however everything returned from LDAP is lower case for me in the PHP arrays 
        # and it is case sensitive
        'fullname'              => array('givenname', 'sn')</pre>

With this, you can set up something like `openid.example.com/username` to authenticate users with OpenID. There's also a way to have a complete subdomain such as `username.example.com` which can delegate the authentication to the other OpenID domain though I haven't tried to configure this.