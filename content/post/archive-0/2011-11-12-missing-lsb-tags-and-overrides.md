---
title: missing LSB tags and overrides
author: wiloon
type: post
date: 2011-11-12T07:34:17+00:00
url: /?p=1502
bot_views:
  - 12
views:
  - 7
categories:
  - Linux

---
update-rc.d tomcat defaults

pop out warning message

missing LSB tags and overrides

add the following lines into the script

<pre>### BEGIN INIT INFO
# Provides: tomcat
# Required-Start: $remote_fs $syslog
# Required-Stop: $remote_fs $syslog
# Default-Start: 2 3 4 5
# Default-Stop: 0 1 6
# Short-Description: Start tomcat at boot time
# Description:       Enable service provided by tomcat.
### END INIT INFO</pre>