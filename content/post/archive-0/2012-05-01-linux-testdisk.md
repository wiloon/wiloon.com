---
title: Linux Testdisk
author: wiloon
type: post
date: 2012-05-01T11:11:34+00:00
url: /?p=3074
categories:
  - Hardware
  - Linux

---
linux下超帅的分区表修复软件.以前用过n多的windows的分区表修复软件，没想到linux中有这么好用这么方便的修复软件，速度那叫一个快啊。。没有见到这个分区表修复软件以前我都白活了。。。。。

<div>
  <p>
        好了，这个软件叫testdisk.很帅的。。
  </p>
  
  <p>
    如果你是使用修复光碟,就下载一个这个软件到电脑中,如果是恢复usb的disk直接
  </p>
  
  <p>
    #sudo apt-get install testdisk
  </p>
  
  <p>
    使用的话先sudo testdisk
  </p>
  
  <p>
    <strong>  1.选择Create来进行分析</strong>
  </p>
  
  <p>
    Use arrow keys to select, then press Enter key:<br /> [ Create ]  Create a new log file<br /> [ Append ]  Append information to log file<br /> [ No Log ]  Don&#8217;t record anything
  </p>
  
  <p>
    <strong>2.然后选择testdisk中你要修复的硬盘，回车</strong>
  </p>
  
  <p>
    Select a media (use Arrow keys, then press Enter):<br /> Disk /dev/sda &#8211; 160 GB / 149 GiB &#8211; ATA HITACHI HTS54251<br /> Disk /dev/sdb &#8211; 3272 MB / 3121 MiB &#8211; SM324BC USB DISK<br /> <strong>    3.选择testdisk修复的平台,我们是Intel的，所以选择他</strong>
  </p>
  
  <p>
    Please select the partition table type, press Enter when done.<br /> [Intel  ]  Intel/PC partition<br /> [EFI GPT]  EFI GPT partition map (Mac i386, some x86_64&#8230;)<br /> [Mac    ]  Apple partition map<br /> [None   ]  Non partitioned media<br /> [Sun    ]  Sun Solaris partition<br /> [XBox   ]  XBox partition<br /> [Return ]  Return to disk selection
  </p>
  
  <p>
    <strong>4.使用testdisk分析,现在选择Analyse进行分析</strong>
  </p>
  
  <p>
    [ Analyse  ]  Analyse current partition structure and search for lost partitions<br /> [ Advanced ]  Filesystem Utils<br /> [ Geometry ]  Change disk geometry<br /> [ Options  ]  Modify options<br /> [ MBR Code ]  Write TestDisk MBR code to first sector<br /> [ Delete   ]  Delete all data in the partition table<br /> [ Quit     ]  Return to disk selection
  </p>
  
  <p>
    <strong> 5.见到了没，基本所有的分区都出来了,直接回车就好了,默认直接回车是快速扫描.</strong>
  </p>
  
  <p>
    *=Primary bootable  P=Primary  L=Logical  E=Extended  D=Deleted<br /> [Quick Search]  [ Backup ]
  </p>
  
  <p>
    然后因为没用vista,所以选择n。
  </p>
  
  <p>
    Should TestDisk search for partition created under Vista ? [Y/N] (answer Yes if<br /> unsure)<br /> N
  </p>
  
  <p>
    <strong>    6.进入,见到你的表区表了吧。</strong>
  </p>
  
  <p>
    Disk /dev/sda &#8211; 160 GB / 149 GiB &#8211; CHS 19457 255 63<br /> Partition               Start        End    Size in sectors<br /> * HPFS &#8211; NTFS              0   1  1  1567 254 63   25189857<br /> L FAT32 LBA             1568   2  1  5097 254 63   56709324 [NO NAME]<br /> L Linux Swap            5098   1  1  5221 254 63    1991997<br /> L Linux                 5222   1  1  7298 254 63   33366942<br /> L Linux                 7299   1  1 19456 254 63  195318207
  </p>
  
  <p>
    Structure: Ok.  Use Up/Down Arrow keys to select partition.<br /> Use Left/Right Arrow keys to CHANGE partition characteristics:<br /> *=Primary bootable  P=Primary  L=Logical  E=Extended  D=Deleted<br /> Keys A: add partition, L: load backup, T: change type, P: list files,<br /> Enter: to continue<br /> NTFS, 12 GB / 12 GiB<br /> 你还可以按p进入一下，看看文件是不是你想要的那些，然后下面会显示文件系统多大,什么系统.
  </p>
  
  <p>
    我进入到这个地方时，基本找出来了，不需要在修改什么了，如果和你的分区不一样,那可能还需要使用Deeper search的功能.我的成功修复了，所以直接按write直接进行写到分区表中修复.
  </p>
</div>