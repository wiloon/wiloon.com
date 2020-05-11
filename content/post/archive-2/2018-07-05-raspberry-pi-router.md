---
title: raspberry pi router
author: wiloon
type: post
date: 2018-07-05T05:58:32+00:00
url: /?p=12400
categories:
  - Uncategorized

---
http://www.embeddedlinux.org.cn/emb-linux/entry-level/201703/18-6294.html

<pre><code class="language-bash line-numbers">sudo -i
apt-get install dnsmasq hostapd
vim /etc/dhcpcd.conf
# 并在文件的最后一行添加以下内容
denyinterfaces wlan0

systemctl stop networking.service
systemctl disable networking.service

vim /etc/systemd/network/eth0.network
[Match]
Name=eth0
[Network]
DHCP=yes

vim /etc/systemd/network/wifi.network
[Match]
Name=wlan0
[Network]
Address=192.168.55.1/24

systemctl start systemd-networkd
systemctl enable systemd-networkd
</code></pre>

<pre><code class="language-bash line-numbers">vim /etc/hostapd/hostapd.conf
interface=wlan0
ssid=ssid0
hw_mode=g
channel=7
wmm_enabled=0
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
# ignore_broadcast_ssid=1
wpa=2
wpa_passphrase=password0
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP
</code></pre>

<pre><code class="language-bash line-numbers">vim /etc/default/hostapd
DAEMON_CONF="/etc/hostapd/hostapd.conf"
</code></pre>

systemctl restart hostapd

<pre><code class="language-bash line-numbers">sudo mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig
sudo vim /etc/dnsmasq.conf
interface=wlan0                                     # Use interface wlan0
listen-address=192.168.55.1                         # Explicitly specify the address to listen on
bind-interfaces                                     # Bind to the interface to make sure we aren't sending things elsewhere
server=223.5.5.5                                    # Forward DNS requests to Google DNS
domain-needed                                       # Don't forward short names
bogus-priv                                          # Never forward addresses in the non-routed address spaces.
dhcp-range=192.168.55.10,192.168.55.20,12h          # Assign IP addresses between $1 and $2 with a 12 hour lease time
</code></pre>

vim /etc/sysctl.conf
  
net.ipv4.ip_forward=1
  
重启后生效

<pre><code class="language-bash line-numbers">sudo iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
sudo iptables -A FORWARD -i eth0 -o wlan0 -m state --state RELATED,ESTABLISHED -j ACCEPT
sudo iptables -A FORWARD -i wlan0 -o eth0 -j ACCEPT
</code></pre>

<pre><code class="language-bash line-numbers">iptables-save &gt; /etc/iptables.ipv4.nat
vim /etc/rc.local
# 在这个文件的exit 0这行的上面加入下面的内容：

iptables-restore &lt; /etc/iptables.ipv4.nat
</code></pre>

https://www.raspberrypi.org/documentation/configuration/wireless/access-point.md
  
https://blog.csdn.net/u012313335/article/details/73992102
  
https://gist.github.com/snakevil/7d7af1d8ca2c739e3fedc5b15eb8e4aa
  
bridge
  
http://www.instructables.com/id/Use-Raspberry-Pi-3-As-Router/
  
https://wireless.wiki.kernel.org/en/users/Drivers/ath10k/configuration
  
https://www.raspberrypi.org/forums/viewtopic.php?t=80299