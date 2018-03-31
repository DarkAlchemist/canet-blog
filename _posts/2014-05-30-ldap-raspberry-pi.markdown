---
title: nss_ldap Raspberry Pi Issues
date: '2014-05-30 17:59:51'
---

This is more a note for myself than anything else. Whilst setting up OpenLDAP and integration with `PAM` and `nsswitch.conf`, I ran into a strange issue where `getent passwd` wouldn't return any of the LDAP results, even using the same configuration that worked on another host. I tracked it down to the symlink `/lib/libnss_ldap.so.2` not existing and needing to point at `nss_ldap.so`. This was a bit of a strange situation as the symlink on my x86-64 based system links elsewhere so I am assuming it is a problem that only exists on arm architectures.