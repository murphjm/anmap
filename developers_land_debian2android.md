# Introduction #

This is not one hundred percent instruction to start debian on any android device, but it would be that in a short time.
Actually, simply no way to verify, but in fact it should be so already.
This text is translated and composed from [1](http://4pda.ru/forum/index.php?showtopic=223191) [2](http://lanrat.com/android/debian) [3](http://www.androidfanatic.com/community-forums.html?func=view&catid=9&id=1615).

You need <font color='red'>root</font> right! And you need linux to make your own image.
Or you can just download one below.
For windows user easiest way to make own image is to download livecd.

There are two way for dealing with the terminal device:
  * With [ConnectBot](http://code.google.com/p/connectbot/), right on device
  * With adb, from Android SDK


I'm not sure, but you should use adb, as much easier to write commands by the normal human keyboard.

So get started!

## Debian image (debootstrap) ##
Installing:
```
$ su -s
# apt-get install debootstrap
```

Create an image with ext2 file system. It's not recommended to set size more than 2Gb(2147483648 bytes):
```
# dd if=/dev/zero of=debian.img seek=2147483647 bs=1 count=1
# mke2fs -F debian.img
```

Mounting:
```
# mkdir debian
# mount -o loop debian.img debian/
```

Look at [A Brief History of Debian](http://www.debian.org/doc/manuals/project-history/index.en.html) and chose a latest debian release, also look at your current linux kernel version.

Installing minimal debian linux of arm architecture([Debian “squeeze”](http://www.debian.org/releases/stable/) June 25th, 2011):
```
# debootstrap --verbose --arch armel --foreign squeeze debian 
```

Unmounting and cleaning:
```
# umount debian/
# rm -r debian/
```

This is the end.
For windows user - linux unrequired any more.

## Boot script ##

Create an script file to start debian, named bootdebian or bootd, whatever.

For the first you should to know a path to folder /system:
```
adb shell mount
```

Or just connect to localhost with [ConnectBot](http://code.google.com/p/connectbot/) and type:
```
$ mount
```

Looking for string with "/system" and get first path (for example /dev/block/stl9)

Writing in our start debian script file(bootdebian) next strings:
```
### Remember this path(/dev/block/stl9 /system).

export system_mount_point=/dev/block/stl9
echo "Mounting system as R/W"
mount -o remount,rw -t yaffs2 $system_mount_point /system

### Strings for mount:

echo "Setting some stuff up.."
export bin=/system/bin
export img_path=/mnt/sdcard/external_sd
export img=$img_path/debian.img
export mnt=/data/local/debian
export loop_device=/dev/block/loop5
export PATH=$bin:/usr/bin:/usr/sbin:/bin:$PATH
export TERM=linux
export HOME=/root
[ ! -e $mnt ] && mkdir -p $mnt
[ ! -e $img_path ] && mkdir -p $img_path

### Strings for mounting image on loop device, and configure network:

echo "Mounting the Linux Image"
mknod $loop_device b 7 0
losetup $loop_device $img
mount -t ext2 -o noatime,nodiratime $loop_device $mnt
mount -t devpts devpts $mnt/dev/pts
mount -t proc proc $mnt/proc
mount -t sysfs sysfs $mnt/sys

echo "Setting Up Networking"
sysctl -w net.ipv4.ip_forward=1
echo "nameserver 8.8.8.8" > $mnt/etc/resolv.conf
echo "nameserver 8.8.4.4" >> $mnt/etc/resolv.conf
echo "127.0.0.1 localhost" > $mnt/etc/hosts

### Strings for mount memory card:

echo "Mounting sdcard and emmc in /mnt"
[ ! -e $mnt/mnt/external_sd ] && mkdir -p $mnt/mnt/external_sd
busybox mount --bind /mnt/sdcard/external_sd $mnt/mnt/external_sd
[ ! -e $mnt/mnt/sdcard ] && mkdir -p $mnt/mnt/sdcard
busybox mount --bind /mnt/sdcard/ $mnt/mnt/sdcard

### Strings for entering in image by chroot:

echo "Entering CHROOT "
echo " "
chroot $mnt /bin/bash

### When exit, unmount everything.
### That's why you should stop work with debian by "exit" command:

echo "Shutting down CHROOT"
umount $mnt/mnt/emmc
umount $mnt/mnt/sdcard
sysctl -w net.ipv4.ip_forward=0
umount $mnt/dev/pts
umount $mnt/proc
umount $mnt/sys
umount $mnt
losetup -d $loop_device
mount -o remount,ro -t yaffs2 $system_mount_point /system
```
[Download example bootdebian script.](http://anmap.googlecode.com/svn/bootdebian)

That's all. Boot script is ready.

Put it and image to sdcard:
```
adb push debian.img /mnt/sdcard/external_sd
adb push bootdebian /mnt/sdcard/external_sd
```



## Finish ##

You need to finish installation and update debian system. Do not forget to stop work with debian by "exit" command!
```
adb shell
$ su
# mount -o remount,rw -t yaffs2 /dev/block/stl9 /system
# cat /sdcard/bootdebian > /system/xbin/bootdebian
# chmod 777 /system/xbin/bootdebian
# bootdebian

# /debootstrap/debootstrap --second-stage
# echo 'deb http://ftp.debian.org/debian/ squeeze main contrib non-free' > /etc/apt/sources.list
# echo 'deb-src http://ftp.debian.org/debian/ squeeze main contrib non-free' >> /etc/apt/sources.list 
# echo '#deb http://mirror.yandex.ru/debian squeeze main'  >> /etc/apt/sources.list 
# echo '#deb-src http://mirror.yandex.ru/debian squeeze main' >> /etc/apt/sources.list 
# apt-get autoclean
# apt-get update
# exit
```

P.S. Chose nearest debian mirror from [list](http://www.debian.org/mirror/list).

_Download debian image with second stage._
  * MD5(debian.img)=
  * SHA(debian.img)=

## X11 Up ##

You should use light windows manager, for example lxde with icewm.

Start debian:
```
adb shell
$ su
# bootdebian
```

Installing lxde, icewm and tightvncserver:
```
# apt-get install lxde
# apt-get install tightvncserver
# apt-get install icewm
```

Configure vncserver, set -geometry to screen resolution and type password.
```
# export USER=root
# vncserver -geometry 800x480
```

Now it is need to stop vncserver and create configure files:
```
# vncserver -kill :1
# touch /root/.Xresources
# vi /root/.vnc/xstartup
```

Edit xstartup script:
```
#!/bin/bash
xrdb /root/.Xresources
xsetroot -solid grey
icewm &
lxsession
```


Create autorun script:
```
# vi /root/.bashrc
```

Adding strings:
```
export USER=root
cd /
rm -r -f tmp
mkdir tmp
cd /
vncserver -kill :1
vncserver -kill :2
vncserver -geometry 800x480
```

Save all and exit:
```
# exit
```

It is required vnc client to connect to vncserver. Look at [android-vnc](http://code.google.com/p/android-vnc-viewer/)

Disconnect usb and reboot.

Connect with [ConnectBot](http://code.google.com/p/connectbot/) and type:
```
$ su
# bootdebian
```

If all good, the output would be looks like:
```
"New 'X' desktop is localhost:1".
```

<a href='http://www.enlightenment.org/'>

<img src='http://www.enlightenment.org/p/about/d/phone.png' align='right'>

Now start <a href='http://code.google.com/p/android-vnc-viewer/'>android-vnc</a>.<br>
<br>
Use next settings:<br>
<ul><li>Your password<br>
</li><li>No username<br>
</li><li>Address is localhost<br>
</li><li>Port - 5901<br>
</li><li>In color format set 24-bit color<br>
</li><li>Force full-screen,<br>
</li><li>Format change to auto.</li></ul>

Push connect button.<br>
<br>
Voila, starting lxde.<br>
<br>
<br>
If you are lucky happy owner of system with processor faster than 1Ghz, it is strongly recommended to look at <a href='http://www.enlightenment.org/'>e17</a> windows manager.<br>
<br>
<br>
Stopping <a href='http://code.google.com/p/android-vnc-viewer/'>android-vnc</a> and enter to <a href='http://code.google.com/p/connectbot/'>ConnectBot</a>, type:<br>
<pre><code># exit<br>
</code></pre>

<i>Download debian image with X.</i> (not done yet)<br>
<ul><li>MD5(debianX.img)=<br>
</li><li>SHA(debianX.img)=</li></ul>

<h2>Developer's</h2>

Entering in debian by  <a href='http://code.google.com/p/connectbot/'>ConnectBot</a> and type:<br>
<pre><code>$ su<br>
# bootdebian<br>
# apt-get install g++ make <br>
</code></pre>

Now, you can get and compile whatever you want.