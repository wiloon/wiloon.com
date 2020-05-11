---
title: 'huawei vpn, sslvpn, secoclient  in archlinux'
author: wiloon
type: post
date: 2020-02-01T03:59:45+00:00
url: /?p=15467
categories:
  - Uncategorized

---
官方只提供了ubuntu版本，用以下方式可以在archlinux上使用。

<pre><code class="language-bash line-numbers"># seco client 依赖ubuntu的arch命令， 模拟arch命令返回x86_64
echo "echo x86_64" &gt; /usr/bin/arch
chmod u+x /usr/bin/arch

# install seco client
./secoclient-linux-64-6.0.2.run

# 启动后台服务
cd /usr/local/SecoClient/promote
./SecoClientPromoteService -d

# 启动secoclient UI
cd /usr/local/SecoClient/
./SecoClient

# in crostini
export WAYLAND_DISPLAY=wayland-0
# user id 使用非0数字(非root的已有用户id,如1000,填0 时，secoclient无法启动)
export XDG_RUNTIME_DIR=/run/user/&lt;user id&gt;
/opt/google/cros-containers/bin/sommelier -X ./SecoClient
</code></pre>