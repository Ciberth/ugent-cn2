# Chapter 3 Devices

## Device Files
_(3.1)_

Device files are sometimes called device nodes. Device files are in the /dev directory. To identify a device use `ls -l`. When you see a _b_, _c_, _p_ or _s_ the file is a device. These lettes stand for _block, character, pipe_ and _socket_ respectively. 

### Block device

Programs access data from a block device in fixed chunks. A block device's total size is fixed and easy to index. Processes have random access to any block in the device with the help of the kernel.

### Character device

Character devices work with data streams. You can only read chars from or write chars to character devices. They don't have a size. Printers directly attached to your computer are represented by character devices. During character device interaction, the kernel cannot back up and reexamine the data stream after it has passed data to a device or process.

### Pipe device

_Named pipes_ are like character devices, with another process at the other end of the I/O stream instead of kernel driver.

### Socket device

_Sockets_ are often found outside the /dev directory. Socket files represent Unix domain sockets. 


## The sysfs Device Path
_(3.2)_

2 Problems:
- The name of the device doesn't say a lot
- The kernel assigns devices in the order in which they are found, so a device may have a different name between reboots. 

The **sysfs interface** is an uniform view based on their actual hardware attributes. The base path for devices is `/sys/devices`. An example for a SATA hard disk (/dev/sda) is `/sys/devices/pci0000:00/0000:00:1f.2/host0/target0:0:0/0:0:0:0/block/sda`.

Both paths have different purposes. The /dev file is there so that the user processes can use the device, whereas the /sys/devices path is used to view information and manage the device. 

/sys/block should contain all of the block devices, those are however symbolic links. Run `ls -l /sys/block` to reveal the true sysfs paths. 

`udevadm info --query=all --name=/dev/sda` can be used to show the path and other attributes. 


## dd and Devices
_(3.3)_

The program `dd` is useful when working with block and character devices. The sole function is to read from an input file or stream and write to an output file or stream (possibly with some encoding). 

`dd` copies data in blocks of a fixed size. An example is `dd if=/dev/zero of=new_file bs=1024 count=1`. This will copy a 1024-byte block from /dev/zero (a continuous stream of zero bytes) to a new_file.

> Note the lack of dash characters. That is because dd is based on an old IBM JCL style. Use the option=value method instead.


## Device Name Summary
_(3.4)_

Ways to find the name of a device:
 
* using `udevd` (`udevadm`)
* in /sys directory
* guess with output of the `dmesg`
* output of `mount` command (if already visible)
* `cat /prov/devices` to see block and character devices (each line has a number=major number and a name)

> Note: Only the first is reliable.

### Hard Disks: /dev/sd*

* Entire disks = /dev/sda & /dev/sdb
* Partitions = /dev/sda1 & /dev/sda2

The sd stands for SCSI (=Small Computer System Interface) disk. Therefore you can use `lsscsi` to list the SCSI devices on your system. Optical drives will be labeled /dev/sr0, these are read only. To (re)write one will use generic devices such as /dev/sg0.

Terminals are devices for moving characters between a user process and an I/O device. Examples are /dev/tty1 (first virtual console) and /dev/pts/0 (first pseduoterminal device). The /dev/pts directory itself is a dedicated filesystem.

> ALT-F1 (and F2, F3...) can be used to switch between consoles. Another command to switch to tty1 for example is `chvt 1`.

### Creating Device Files

> In modern Linux systems, you do not create your own device files; this is done with devtmpfs and udev. 

However it is interesting (and might help you) to know how it was once done. The `mknod` command creates a device. Make sure you know the name, and both its major and minor numbers. To create /dev/sda1 one should use: `mknod /dev/sda1 b 8 2`. The _b 8 2_ specifies a block device with a major number _8_ and a minor number _2_. _c_ or _p_ can be used to create character or named pipe devices. 

## udev
_(3.5)_

The Linux kernel sends notifications to a user-space process (_udevd_) upon detecting a new device on the system. Afterwards its this user-space process that examines the device's characteristics and creates a device file. This was the theory! In practice there is a problem. Device files are necessary early in the boot procedure therefore udevd had to start early. 

The devtmpfs filesystem was developed in response to that problem. The kernel creates device files as necessary but it also notifies udevd that a new device is available. After that udevd does not create the device files, but it does perform device initialization and process notification. Udevd creates symbolic links in /dev to further identify devices.

### udevd operation and configuration

The udevd daemon operates as follows:

1. The kernel sends udevd a notification event, called a uevent, through an internal network link.
2. udevd loads all of the attributes in the uevent.
3. udevd parses its rules, and it takes actions or sets more attributes based on those rules.

The rules files are in the /lib/udev/rules.d and /etc/udev/rules.d directories. 

The _udevadm_ program is an administration tool for udevd. For example reloading udevd rules and triggering events. But you can search, explore and monitor system devices as well. An example is `udevadm info --query=all --name=/dev/sda`.

To monitor uevents with udevadm use `udevadm monitor`. Note that there are two copies of each message in this output because the deafult beahvior is to print both the incoming message from the kernel and the message that udevd sends. Use `--kernel` or `--udev` if you only want one entry. Use `--property` if you want to see the whole incoming uevent including attributes.

You can also filter events by subsystem. For example, to seeonly kernel messages pertaining to changes in the SCSI subsystem use: `udevadm monitor --kernel --subsystem-match=scsi`.









