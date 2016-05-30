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
* The name of the device doesn't say a lot
* The kernel assigns devices in the order in which they are found, so a device may have a different name between reboots. 

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






