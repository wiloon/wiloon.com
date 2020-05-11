---
title: emacs elpa
author: wiloon
type: post
date: 2017-10-27T04:57:46+00:00
url: /?p=11315
categories:
  - Uncategorized

---
https://elpa.emacs-china.org/index-en.html

<pre><code class="language-bash line-numbers">emacs ~/.emacs
(setq package-archives '(("gnu"   . "http://elpa.emacs-china.org/gnu/")
                         ("melpa" . "http://elpa.emacs-china.org/melpa/")))

M-x list-packages
</code></pre>

Type M-x list-packages to open the package list. Press ‘i’ to mark for installation, ‘u’ to unmark, and ‘x’ to perform the installation. Press ‘RET’ to read more about installing and using each package.

https://www.emacswiki.org/emacs/InstallingPackages