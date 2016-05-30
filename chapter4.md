# Chapter 4 Disks and Filesystems

Partitions are subdivisions of the whole disk. The kernel presents each partition as a block device, just as it would an entire disk. Partitions are defined on a small area of the disk called a partition table. The next layer after the partition is the filesystem. 


## Partitioning Disk Devices
_(4.1)_

Traditional table is the one found inside the MBR (= Master Boot Record), a newer standard is GPT (Globally Unique Identifier Partition Table).

Some partitioning tools are:
* (g)parted (both MBR and GPT)
* fdisk (for MBR)
* gdisk (for GPT)

You can view your system's partition table with `parted -l`.
- **A primary partition** is a normal subdevision of the disk.
- The basic MBR has a limit of four primary partitions.
- If you want more designate one partition as an **extended partition**.
- Next you subdivide the extended partition into **logical patitions**.

To get the system ID for an MBR one can use `fdisk -l`.

With fdisk you design your new partition table before making the actual changes to the disk. Fdisk only makes the changes as you exit the program. With parted everything is "live" as you issue the commands. So be aware you don't get the chance to review the partion table before you change it with parted. Both modify the partitions entirely in user space. 

Eventually the kernel must read the partition table in order to present the partitons as block devices. The _fdisk_ utility does it as follows: after modifying the partition table, fdisk issues a single system call on the disk to tell the kernel that it should reread the partition table. The kernel then generates debugging output that you van view with dmesg. In comparison the _parted_ tools do not use this disk-wide system call. Instead they signal the kernel when individual partitions are altered. After processing a single partition change, the kernel does not produce debugging output.

To see partition changes you can use:
* `udevadm monitor --kernel`
* check _/proc/partitions_ for full partition information
* check _/sys/block/device/_ for altered partition system interfaces or _/dev_ for altered partition devices.

To force the kernel to reload the partition table on _/dev/sdf_ run this: `blockdev --rereadpt /dev/sdf`.

## Filesystems
_(4.2)_

The filesystem is a form of database. It supplies the structure to transform a simple block device into the sophisticated hierarchy of files and subdirectories that users can understand. 

List of filesystem types:
* ext4
* ISO 9660 (CD-ROM)
* FAT
* HFS+

**Creating a filesystem** will be done in user space (just like partitioning). A user-space process can directly access and manipulate a block device. Example to create an ext4 partition on /dev/sdf2 is `mkfs -t ext4 /dev/sdf2`.

When creating a filesystem, mkfs prints information such as the **superblock**. This is a key component at the top level of the filesystem database. 

**Mounting a filesystem** is a synonym for attaching a filesystem. In order to mount a filesystem you must know the filesystem's device, type and the _mount point_.

Use `mount` to know the current status of your system. Each line corresponds with one currently mounted filesystem.

To mount a filesystem use `mount -t type device mountpoint`. For example `mount -t ext4 /dev/sdf2 /home/extra`. Use `umount mountpoint` to detach. 

`blkid` (blockid) can be used to get a list of devices and corresponding filesystems and UUIDs (=Universally Unique Identifier) on your system. To mount with an UUID one can use `mount UUID=..-.. /home/extra`.

Generally Linux buffers writes to the disk. When you unmount a filesystem the kernel will automatically synchronize with the disk. If you want to force this use the `sync` command. 

Some options for mounting are:
* -r read-only mode
* -n ensures that mount does not try to update the system runtime mount database
* -t type of filesystem
* -o long options
* ro/rw read-only/read-write
* conv=auto DOS newline format to Unix style
* (no)exec
* (no)suid

Remounting can be done with `mount -n -o remount /`

*/etc/fstab*



4.4 niet in slides

## Extra (slides + notities)

TODO