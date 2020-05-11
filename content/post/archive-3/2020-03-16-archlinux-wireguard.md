---
title: wireguard
author: wiloon
type: post
date: 2020-03-15T16:20:26+00:00
url: /?p=15763
categories:
  - Uncategorized

---
### install

#### arch

<pre><code class="language-bash line-numbers">pacman -Syu
pacman -S wireguard-tools
pacman -S wireguard-arch

lsmod | grep wireguard
sudo modprobe wireguard
</code></pre>

#### debian

<pre><code class="language-bash line-numbers">echo "deb http://deb.debian.org/debian/ unstable main" | sudo tee /etc/apt/sources.list.d/unstable-wireguard.list
printf 'Package: *\nPin: release a=unstable\nPin-Priority: 90\n' | sudo tee /etc/apt/preferences.d/limit-unstable
apt update
apt install wireguard
</code></pre>

### peer A

<pre><code class="language-bash line-numbers">umask 077
wg genkey &gt; privatekey
wg pubkey &lt; privatekey &gt; publickey
wg genpsk &gt; preshared

sudo ip link add dev wg0 type wireguard
sudo ip addr add 192.168.53.1/24 dev wg0
sudo wg set wg0 private-key ./privatekey
sudo wg set wg0 listen-port 9000
# peer b 有确定的端口和IP时， 可以配置endpoint
sudo wg set wg0 peer &lt;PEER_B_PUBLIC_KEY&gt; persistent-keepalive 25 allowed-ips 192.168.53.2/32 endpoint 192.168.50.115:9000
# 做为服务端使用时，peer B的ip 和端口一般是动态的，不配置endpoint
sudo wg set wg0 peer &lt;PEER_B_PUBLIC_KEY&gt; persistent-keepalive 25 allowed-ips 192.168.53.2/32
ip link set wg0 up
</code></pre>

### peer B

<pre><code class="language-bash line-numbers">sudo ip link add dev wg0 type wireguard
sudo ip addr add 192.168.53.2/24 dev wg0
sudo wg set wg0 private-key ./privatekey
# 配置监听端口，监听peer A发起的连接 请求，仅作为客户端使用时，可以不配置监听
sudo wg set wg0 listen-port 9000
# allowed-ips 0.0.0.0/0 peer_B 所有的ip包都 会被 发往 peer_A
sudo wg set wg0 peer PEER_A_PUBLIC_KEY persistent-keepalive 25 allowed-ips 0.0.0.0/0 endpoint 192.168.50.215:9000
ip link set wg0 up

</code></pre>

### remove peer

<pre><code class="language-bash line-numbers">wg set wg0 peer PEER_A_PUBLIC_KEY remove
</code></pre>

### 保存配置到文件

<pre><code class="language-bash line-numbers">wg showconf wg0 &gt; /etc/wireguard/wg0.conf
</code></pre>

### chromeos>crostini

crostini 不支持wireguard 类型的网络设备， 不能直接使用wireguard, 需要安装tunsafe
  
<https://tunsafe.com/user-guide/linux>

<pre><code class="language-bash line-numbers">git clone https://github.com/TunSafe/TunSafe.git
cd TunSafe
sudo apt-get install clang-6.0
make
sudo make install
sudo tunsafe start  TunSafe.conf
sudo tunsafe start -d TunSafe.conf
</code></pre>

#### tunsafe 配置文件

<pre><code class="language-bash line-numbers">[Interface]
PrivateKey = &lt;private_key&gt;
DNS = 192.168.50.1
BlockDNS = true
# 设置虚拟网卡的内网地址（可选子网掩码）
Address = 192.168.53.3/24
[Peer]
PublicKey = &lt;public_key&gt;
# 目标地址是192.168.53.1 的会通过vpn发送到服务端
AllowedIPs = 192.168.53.1/24
# 所有ip包都 会发往 vpn服务端
AllowedIPs = 0.0.0.0/0
Endpoint = &lt;server_ip0:server_port0&gt;
PersistentKeepalive = 25
</code></pre>

### systemd-networkd

systemd-networkd-wait-online.service
  
systemd-resolvconf
  
openresolv

### iptables

<pre><code class="language-bash line-numbers">iptables -A FORWARD -i wg0 -j ACCEPT
iptables -t nat -A POSTROUTING -o wlp1s0 -j MASQUERADE
</code></pre>

https://www.wireguard.com/install/
  
https://www.linode.com/docs/networking/vpn/set-up-wireguard-vpn-on-debian/
  
https://blog.mozcp.com/wireguard-usage/

<blockquote class="wp-embedded-content" data-secret="3eFM6gPGdn">
  <p>
    <a href="https://mine260309.me/archives/1697">自己搭建WireGuard给Android用</a>
  </p>
</blockquote>

<iframe title="&#8220;自己搭建WireGuard给Android用&#8221; &#8212; This is Mine" class="wp-embedded-content" sandbox="allow-scripts" security="restricted" style="position: absolute; clip: rect(1px, 1px, 1px, 1px);" src="https://mine260309.me/archives/1697/embed#?secret=3eFM6gPGdn" data-secret="3eFM6gPGdn" width="600" height="338" frameborder="0" marginwidth="0" marginheight="0" scrolling="no"></iframe>
  
https://wiki.debian.org/Wireguard
  
https://docs.linuxconsulting.mn.it/notes/setup-wireguard-vpn-on-debian9
  
https://github.com/wgredlong/WireGuard/blob/master/2.%E7%94%A8%20wg-quick%20%E8%B0%83%E7%94%A8%20wg0.conf%20%E7%AE%A1%E7%90%86%20WireGuard.md