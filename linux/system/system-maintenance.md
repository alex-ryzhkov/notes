# system maintenance

## systemd logs take too much space
`journalctl --vacuum-size=200M`
or put `SystemMaxUse=100M` into `/etc/systemd/journald.conf`
[source](https://askubuntu.com/questions/1012912/systemd-logs-journalctl-are-too-large-and-slow)

## ugly fonts (like in comments on onliner)
install `tex-gyre-fonts` and `ttf-ms-fonts`

## change display resolution
`xrandr -s WIDTHxHEIGHT`

## grub wrong resolution
1. boot in grub command line
2. type `videoinfo` to check resolutions that are supported
3. go to `etc/default/grub` find `GRUB_GFXMODE` and set it to supported resolution
4. run `# update-grub` in terminal

## Hide GRUB unless the Shift key is held down
https://gist.githubusercontent.com/anonymous/8eb2019db2e278ba99be/raw/257f15100fd46aeeb8e33a7629b209d0a14b9975/gistfile1.sh [1]
In order to achieve the fastest possible boot, instead of having GRUB wait for a timeout, it is possible for GRUB to hide the menu, unless the Shift key is held down during GRUB's start-up.
In order to achieve this, you should add the following line to `/etc/default/grub`:
`GRUB_FORCE_HIDDEN_MENU="true"`
Then create the file in [1], make it exectuable, and regenerate the grub configuration:
```
sudo chmod a+x /etc/grub.d/31_hold_shift
sudo grub-mkconfig -o /boot/grub/grub.cfg
```
Note: This setup uses keystatus to detect keypress event so it may not work on some machines.
[Source](https://wiki.archlinux.org/index.php/GRUB/Tips_and_tricks#Hide_GRUB_unless_the_Shift_key_is_held_down)

## Make GRUB remember the kernel you chose to boot

```
vim /etc/default/grub
```

```
GRUB_SAVEDEFAULT=true
GRUB_DEFAULT=saved
```

```
update-grub
```
[Source](https://unix.stackexchange.com/a/421650)

## installing linux on the second drive uefi
https://askubuntu.com/questions/726972/dual-boot-windows-10-and-linux-ubuntu-on-separate-hard-drives

## default home dir structure
`.config/user-dirs.dirs`

## how old the system installation is
`sudo tune2fs -l /dev/sdaX | grep 'Filesystem created:'`

## sync system clock
`systemd-timesync.service`

## disable display standby
`xset s off`
