# hddtemp
## if hddtemp cannot detect drive
install `smartmontools`
then edit `usr/share/hddtemp/hddtemp.db` like this
```
########################################
############# My Toshiba HDD
########################################
"TOSHIBA MK6476GSX"   190     C       "TOSHIBA MK6476GSX"
```
1. first position is and hdd model (get it from `hddtemp --debug <device>`)
2. second position is a smart field (use `smartctl`)
3. third is a temperature measurement
4. the fourth one is a comment

[Source](https://evilshit.wordpress.com/2014/09/30/how-to-add-a-hard-disk-to-hddtemp-and-read-the-temperature/)
