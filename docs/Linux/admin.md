# Linux Administration 

## Creating and mounting new partitions 
In order to create a /scratch partition for my Linux server I had to first create 
a partition for a hard drive and then mount it in a new location. The first step
needed for this was made easy by the fdisk program. Initially when running it 
I would get something like this

```
username@machinename:$ sudo fdisk -l
[sudo] password:
Note: sector size is 4096 (not 512)

Disk /dev/sdb: 999.0 GB, 998997229568 bytes
255 heads, 63 sectors/track, 15181 cylinders, total 243895808 sectors
Units = sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk identifier: 0x00000000

Disk /dev/sdb doesn't contain a valid partition table

Disk /dev/sda: 128.0 GB, 128035676160 bytes
255 heads, 63 sectors/track, 15566 cylinders, total 250069680 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x000979b9

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048    19531775     9764864   83  Linux
/dev/sda2        19533822   250068991   115267585    5  Extended
/dev/sda5        19533824    28801023     4633600   82  Linux swap / Solaris
/dev/sda6        28803072   250068991   110632960   83  Linux
```
 
You can see that there are two drives, sda and sdb, the latter of which
does not have a partition table. To create a new partition I simply typed 
`sudo fdisk /dev/sdb` and then I entered the dialogue from the fdisk program.
First I created a new partition accepting most of the defaults, which resulted 
in the following output for `fdisk -l`

```
Note: sector size is 4096 (not 512)

Disk /dev/sdb: 999.0 GB, 998997229568 bytes
208 heads, 2 sectors/track, 586288 cylinders, total 243895808 sectors
Units = sectors of 1 * 4096 = 4096 bytes
Sector size (logical/physical): 4096 bytes / 4096 bytes
I/O size (minimum/optimal): 4096 bytes / 4096 bytes
Disk identifier: 0xc7a7c4cd

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1             256   243895807   975582208   83  Linux

Disk /dev/sda: 128.0 GB, 128035676160 bytes
255 heads, 63 sectors/track, 15566 cylinders, total 250069680 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk identifier: 0x000979b9

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048    19531775     9764864   83  Linux
/dev/sda2        19533822   250068991   115267585    5  Extended
/dev/sda5        19533824    28801023     4633600   82  Linux swap / Solaris
/dev/sda6        28803072   250068991   110632960   83  Linux
```

Note the change in that now /dev/sdb1 appears. The next step was formating the
partition, which can be achieved by typing in the command line

```
sudo mkfs.ext4 /dev/sdb1
```

Then I created a mount point, which in this case is called /scratch and mounting
the new partition sdb1 in that mount point.

```
sudo mkdir /scratch
sudo mount /dev/sdb1 /scratch
```

The final bit was getting my system to mount that partition automatically, by
editing the /etc/fstab file. For this I needed the UUID for my partition. This
can be obtained from 
```
ls -l /dev/disk/by-uuid/
```

Then one must edit the /etc/fstab file with the UUID for sdb1, the mount point and the
type of partition in a line looking very much like the one that follows
```
UUID=31321321zxcgdfsdg-sdfgsdfaadsas31245 /scratch        ext4    errors=remount-ro 0     2`
```

A final detail was to recursively give read+write permissions to everyone.

```
sudo chmod -R 777 /scratch/
```



