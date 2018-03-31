---
title: Flexget
date: '2013-10-09 21:19:57'
tags:
- flexget
---

### Installation

---
#### Requirements

* Python 2.6-2.7

#### Instructions
##### Gentoo
	emerge -av --quiet-build dev-python/pip
	pip install flexget
	
For using with Transmission bittorrent client (it may need uninstalling and reinstalling)
	
	pip install transmissionrpc
    
### Configuration Notes

---
Main configuration file for a user exists at `~/.flexget/config.yml`

* Indentation level. Always use (multiples of) 2 spaces and never use tab-key!
* Plugins are supposed to be indented at the same level (rss, series, download etc). Some plugins may allow other plugins inside of them.
* Missing colons. Pay special attention to these when looking at the examples and documentation
* If text value contains any of `{}[]%:` characters it must be quoted with ''. If you want to pass a number as a text (ie. series 24), value must be quoted with ''