---
title: emacs auto backup
author: wiloon
type: post
date: 2011-04-23T09:05:52+00:00
url: /?p=106
bot_views:
  - 3
categories:
  - Emacs

---
(setq backup-directory-alist &#8216;((&#8220;&#8221; . &#8220;~/backup/emacs/backup&#8221;)))
  
(setq-default make-backup-file t)
  
(setq make-backup-file t)

(setq make-backup-files t)
  
(setq version-control t)
  
(setq kept-old-versions 2)
  
(setq kept-new-versions 10)
  
(setq delete-old-versions t)