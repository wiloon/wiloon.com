---
title: maven ojdbc6
author: wiloon
type: post
date: 2012-12-08T05:49:14+00:00
url: /?p=4863
categories:
  - Uncategorized
tags:
  - Maven

---
[shell]

mvn install:install-file -DgroupId=com.oracle -DartifactId=ojdbc6 -Dversion=11.2.0.3 -Dpackaging=jar -Dfile=/home/wiloon/Downloads/ojdbc6.jar

[/shell]