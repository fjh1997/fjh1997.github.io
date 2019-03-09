---
layout:     post                    # 使用的布局（不需要改）
title:      solution to the problem that Screen only shows partial area on hackintosh 10.14 Mojave  # 标题 
subtitle:   with clover configuration #副标题
date:       2018-03-08              # 时间
author:     fjh1997                 # 作者
header-img: img/Mojave.JPG    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - hackintosh
---

# english version guide

## solution to the problem that Screen only shows partial area on hackintosh 10.14 Mojave.

### My machine configuration
<pre>
<b>Processor</b> Intel Core i7-6700 @ 3.40GHz quad core
<b>Motherboard</b> MSI B150M PRO-VD (MS-7996) (Intel Skylake-S - 100 Series/C230 Series Chipset Family - A148 )
<b>Memory</b> 16 GB (Samsung DDR4 2133MHz / Jinbang DDR4 2133MHz)
<b>Main hard drive</b>Colorful SL300 240GB (240 GB / SSD)
<b>Graphics Card</b> AMD Radeon RX Vega 64 ( 8 GB / AMD )
<b>Display</b> IPS2480 (24 inches)
<b>Optical Disc Drive</b> Virtual Disc Drive
<b>Sound Card</b>  Realtek ALC887 @ Intel High Definition Audio Controller
<b>Network card</b>  Realtek RTL8168/8111/8112 Gigabit Ethernet Controller / MSI
</pre>
### Problems encountered
1. At first, I tried to use ddmac to write the partition, but it always failed. After searched almostly all  methods on the Internet,I just restart computer and it worked!

2. Regardless of which image you use. a lazy installer image or an official original image, you will encounter the following problem when opening the installer.

As shown
![Screen problem](https://github.com/fjh1997/hackintosh-clover/raw/master/WechatIMG3.jpeg)

This problem prevented me from proceeding to the next step.
### Solution 1

Inadvertently , I found that if you press Enter on keyboard,then move the cursor to the upper left corner and start the terminal, execute the following command.

```sh
# shutdown -s now
```

The computer should go to sleep. Next, press the power button, the computer would wake from sleep mode, this time the resolution back to normal. By this way ,you can continue to install.
But I still encounter the following problems.
![Insert Picture description here](https://github.com/fjh1997/hackintosh-clover/raw/master/QQ20190306-1.JPG)
it shows An error occured installling macOs. And a friend advised me not to install with chameleoned lazy image, but to directly install with the official image. I tried， and the installation is successful. but  it is *Just the beginning*.
## Solution 2
After the above  successful installation, I try to open the Mac OS, but unfortunately, this time there is not even a little area shows, but directly black screen. After I worked hard to find a way, I finally found a solution.
It is a very important step : open bios, select the configuration of the windows operating system
![Insert Picture description here](https://github.com/fjh1997/hackintosh-clover/raw/master/WechatIMG0.jpeg)

Check the Windows 8.1/10 WHQL support inside and restart.

![Insert Picture description here](https://github.com/fjh1997/hackintosh-clover/raw/master/WechatIMG2.jpeg)

After  restart, you'll be surprised to find it get a normal display.
## Installation Results
This is the result after I successfully installed Mac OS .And it supports dual screen.
![Insert Picture description here](https://github.com/fjh1997/hackintosh-clover/raw/master/QQ20190306-0.JPG)
My clover configuration:

Https://github.com/fjh1997/hackintosh-clover

reference:
 [1]: Detailed description of clover graphics injection function https://www.cnblogs.com/motoyang/p/5140594.html