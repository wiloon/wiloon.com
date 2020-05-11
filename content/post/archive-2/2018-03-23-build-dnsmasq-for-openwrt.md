---
title: r7800 openwrt
author: wiloon
type: post
date: 2018-03-22T16:08:42+00:00
url: /?p=12034
categories:
  - Uncategorized

---
在ubuntu上安装如下软件包：
  
gcc， g++, binutils, bzip2, flex, python, perl, make,find, grep, diff, unzip, gawk, getopt, subversion, libz-dev and libc 头文件
  
sudo apt-get updatesudo apt-get install gcc g++ build-essential subversion git-core libncurses5-dev zlib1g-dev gawk flex quilt libssl-dev xsltproc libxml-parser-perl mercurial bzr ecj cvs unzip
  
选择编译目标
  
make menuconfig Target System (Qualcomm Atheros IPQ806X) &#8212;>
  
Target Profile (Netgear Nighthawk X4S R7800) &#8212;> 退出保存
  
编译固件
  
make V=s -j4
  
编译完成后
  
ubuntu:~/kunteng-lede$ ls bin/targets/ipq806x/generic/config.seed
  
lede-ipq806x-R7800-squashfs-sysupgrade.tar lede-ipq806x-vmlinux.elflede-ipq806x-device-r7800.manifest
  
lede-ipq806x-squashfs-root.img
  
packageslede-ipq806x-R7800-squashfs-factory.img lede-ipq806x-ubifs-root.img
  
sha256sums其中lede-ipq806x-R7800-squashfs-factory.img即为R7800的工厂固件， lede-ipq806x-R7800-squashfs-sysupgrade.tar为升级固件

download https://downloads.openwrt.org/releases/17.01.4/targets/bcm53xx/generic/lede-sdk-17.01.4-bcm53xx\_gcc-5.4.0\_musl-1.1.16\_eabi.Linux-x86\_64.tar.xz
  
cd openwrt
  
./scripts/feeds update -a
  
./scripts/feeds install dnsmasq
  
make menuconfig

https://wiki.openwrt.org/doc/howto/buildroot.exigence

https://wiki.openwrt.org/doc/howto/obtain.firmware.sdk

<blockquote class="reddit-card" >
  <p>
    <a href="https://www.reddit.com/r/openwrt/comments/74mgvd/need_help_to_build_custom_dnsmasq_package_for/?ref_source=embed&ref=share">Need Help to Build Custom "DNSmasq" Package for Chaos Calmer</a> from <a href="https://www.reddit.com/r/openwrt/">openwrt</a>
  </p>
</blockquote>