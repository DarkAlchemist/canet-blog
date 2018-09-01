---
title: Implementing Samba with LDAP Backend on Gentoo
date: '2016-07-09 20:16:00'
tags:
- samba
- gentoo
- linux
---

## Integrating LDAP into Samba
---

### Installation
---
#### Gentoo
* Enable `sambakr5passwd` and `samba` USE flags for OpenLDAP
* Enable `ldap` USE flag for Samba
	
	1. `USE="smbkrb5passwd samba" emerge openldap`
	2. `USE="ldap" emerge samba`
	3. `emerge net-nds/smbldap-tools`

### Configuration
---
1. Edit `/etc/openldap/slapd.conf` to include the following:

		include /etc/openldap/schema/samba.schema

		access to attrs=userPassword,sambaLMPassword,sambaNTPassword,shadowLastChange,sambaPwdLastSet,sambaPwdMustChange
    	by dn="cn=Samba Admin,ou=SystemAccounts,dc=canet,dc=me,dc=uk" write

		access to *
    	by dn="cn=Samba Admin,ou=SystemAccounts,dc=canet,dc=me,dc=uk" write
    This grants the `Samba Admin` account full access to LDAP once authenticated. 

2. We need to add an OU for the Samba required entries as well as the `Samba Admin` account. To do this, add to the LDIF data the following (use `slapcat > myldif.ldif` to generate an LDIF file including all the current data):

		dn: ou=SystemAccounts,dc=canet,dc=me,dc=uk
		objectClass: organizationalUnit
		objectClass: top
		objectClass: domainRelatedObject
		associatedDomain: canet.co.uk
		ou: SystemAccounts

		dn: cn=Samba Admin,ou=SystemAccounts,dc=canet,dc=me,dc=uk
		givenName: Samba
		sn: Admin
		uid: smbadmin
		cn: Samba Admin
		userPassword: {SSHA}eXKqB5iVZnu+sValdI3Omqbyvt1el6c5
		objectClass: inetOrgPerson
		objectClass: top
		
3. Load the LDIF changes into LDAP with the following (you are free to add the data into LDAP any other way you wish as long as it gets there!)

		rm -rf /var/lib/openldap*
		slapadd -l myldif.ldif
		chown -R ldap:ldap /var/lib/openldap/

4. Edit the Samba config to reflect the following so it knows how to connect to the LDAP server (`/etc/samba/smb.conf`):
		
		# LDAP server location
		passdb backend = ldapsam:ldap://127.0.0.1:389/
		
		# The Base DN
		ldap suffix = dc=canet,dc=me,dc=uk
		
		# The LDAP path to computer, user and group account OUs
		ldap machine suffix = ou=Computers
		ldap user suffix = ou=People
		ldap group suffix = ou=Groups
		
		# Samba Admin account in LDAP to use for Administrative tasks
		ldap admin dn = cn=Samba Admin,ou=SystemAccounts,dc=canet,dc=me,dc=uk
		
		# Not sure what this is for
		ldap delete dn = no
   
		# This is for password synchronisation between the Unix and the Samba password in LDAP. 
		# So if you change your Samba password over smbpasswd or Windows this option changes your Unix/Linux password too.
		ldap password sync = yes

5. Run `smbpasswd -W` to update local secret for accessing the `Samba Admin` account.

6. Run `smbldap-config` 
	* Set `.` where required to generate blank fields. 
	* Set password validation time to 99999

7. Run `smbldap-populate -e server.ldif`. This generates an LDIF file with all the required LDAP users and groups.
	1. Remove entries that already exist and remove the root and nobody accounts. Make sure your Groups OU is set correctly!
	2. Make sure it picks up the correct domain. It picked up my workgroup instead (as it was previously something else). If you run `net getlocalsid` it will give you the domain in capitals.

8. Load the Samba accounts with `ldapadd -x -D "cn=Manager,dc=canet,dc=me,dc=uk" -f server.ldif -W` (this uses the LDAP Admin account to update from the LDIF file)

9. Once done, we need to update existing ldap entries with SAM entries so need to run:
		
		smbldap-usermod -a christakis
	WARNING: If the ACLs in `slapd.conf` are too restrictive, this WON'T work

10. `smbpasswd christakis` should then update both LDAP and Samba passwords as we've set Samba to sync them with `ldap password sync = yes` in the configuration file earlier.
	* At this point, if we update the LDAP password without `smbpasswd`, it won't update the Samba password!!

#### Enable smbk5pwd Overlay
---
This overlay enables the updating of Kerberos and Samba passwords via an LDAP overlay whenever the LDAP password is changed via LDAP. Following the instructions for initial installation, the relevant library should be installed so it should only need configuration

1. Edit `/etc/openldap/slapd.conf`:

		moduleload /usr/lib64/openldap/openldap/smbk5pwd.so
		
		#
		# More configuration…
		#
		# Database definition…
		#
		
		overlay smbk5pwd
		smbk5pwd-enable samba
		smbk5pwd-must-change 0
		
	This enables the overlay in LDAP to propegate the changes to LDAP

2. Edit `/etc/samba/smb.conf`:
		
		ldap password sync = only
		
	This tells Samba to only update the LDAP password and let the LDAP server do the rest (so use the new overlay!)
