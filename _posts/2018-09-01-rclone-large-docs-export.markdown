---
title: Rclone and large Google Docs export support
date: '2018-09-01 09:17:00'
tags:
- google
- gsuite
- rclone
---

Large Google docs above 5-10MB have issues exporting to via rclone for backup purposes. With the release of **rclone v1.42**, there's a new option `--drive-alternate-export` which used a different method to export Google Docs stored in Drive, which allows this to exceed the size limits. Documentation is [here](https://rclone.org/drive/).
