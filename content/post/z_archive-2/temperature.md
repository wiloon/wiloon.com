---
title: 温度监控, lm-sensors, temperature
author: "-"
date: 2018-06-17T03:15:48+00:00
url: /?p=12312
categories:
  - Inbox
tags:
  - reprint
---
## 温度监控, lm-sensors, temperature

### 不用安装其它软件的方法

```bash
cat /sys/class/thermal/thermal_zone0/temp
echo $[$(cat /sys/class/thermal/thermal_zone0/temp)/1000]°

```

### sensors

```bash
apt-get install lm-sensors
# 检测传感器
sh -c "yes|sensors-detect"

# 查看CPU的温度
sensors
```

#### 输出

```r
coretemp-isa-0000
Adapter: ISA adapter
Package id 0:  +57.0°C  (high = +105.0°C, crit = +105.0°C)
Core 0:        +57.0°C  (high = +105.0°C, crit = +105.0°C)
Core 1:        +57.0°C  (high = +105.0°C, crit = +105.0°C)
Core 2:        +57.0°C  (high = +105.0°C, crit = +105.0°C)
Core 3:        +58.0°C  (high = +105.0°C, crit = +105.0°C)

acpitz-acpi-0
Adapter: ACPI interface
temp1:        +27.8°C  (crit = +119.0°C)

nvme-pci-0400
Adapter: PCI adapter
Composite:    +64.8°C  (low  = -273.1°C, high = +84.8°C)
                       (crit = +84.8°C)
Sensor 1:     +64.8°C  (low  = -273.1°C, high = +65261.8°C)
Sensor 2:     +74.8°C  (low  = -273.1°C, high = +65261.8°C)
```

原贴:[http://goodfifagun.pixnet.net/blog/post/21587839](http://goodfifagun.pixnet.net/blog/post/21587839)

現在購買主機板時都會有廠商提供的監控軟體可以使用，而最常使用到到功
 能像溫度監控，系統狀態等等，但是這些軟體都只能在windows 下使用，所
 以如果要在 linux下監控CPU溫度，可以透過 lm-sensor這套軟體來監控。安裝環境:
 ubuntu 8.04.1
  
安裝步驟:

1. 安裝lm-sensors

# apt-get install lm-sensors
  
    2. 設定監控選項

# sensors-detect
  
    通常都是回答yes即可，注意最後一項，例如下面的資訊
  
  
    To load everything that is needed, add this to /etc/modules:
  
  
    #--cut here--

# I2C adapter drivers

# modprobe unknown adapter NVIDIA i2c adapter

# modprobe unknown adapter NVIDIA i2c adapter

# modprobe unknown adapter NVIDIA i2c adapter

 i2c-i801

# Chip drivers

# no driver for Winbond W83L785R/G yet

 lm85
 #--cut here--
  
    3. 出現如上訊息後，載入模組，
 例如我的是 i2c-i801 與 lm85，
  
    # modprobe i2c-i801

# modprobe lm85
  
    4. 之後再輸入: 

# sensors
  
    5. 就會出現cpu溫度之類的監控訊息。
  
  
    adm1027-i2c-3-2e
 Adapter: SMBus I801 adapter at e000
 V1.5: +1.31 V (min = +0.00 V, max = +3.32 V)
 VCore: +1.49 V (min = +0.00 V, max = +2.99 V)
 V3.3: +3.30 V (min = +0.00 V, max = +4.38 V)
 V5: +5.08 V (min = +0.00 V, max = +6.64 V)
 V12: +11.97 V (min = +0.00 V, max = +15.94 V) ALARM
 CPU_Fan: 3941 RPM (min = 0 RPM)
 fan2: 0 RPM (min = 0 RPM)
 fan3: 0 RPM (min = 0 RPM)
 fan4: 1882 RPM (min = 0 RPM)
 CPU Temp: +48.8°C (low = -127.0°C, high = +127.0°C)
 Board Temp: +46.0°C (low = -127.0°C, high = +127.0°C)
 Remote Temp: +45.5°C (low = -127.0°C, high = +127.0°C)
 cpu0_vid: +1.525 V
  