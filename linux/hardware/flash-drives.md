# flash drives

## The Command-Line Way to clean a USB-drive
```
sudo fdisk /dev/sdb
```
(d)elete partitions, make a (n)ew partition, (w)rite changes

```
sudo mkfs -t vfat /dev/sdb1
```

```
sudo eject <device>
```

[Source](https://askubuntu.com/questions/22381/how-to-format-a-usb-flash-drive)

##  automount
use **udiskie** (as a daemon)
