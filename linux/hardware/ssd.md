# ssd

## fstrim doesn't work with /home partition by default
to fix it go to `/usr/lib/systemd/system/fstrim.service` and comment out
`ProtectHome=yes`

## add note about disabling fstrim in fstab and moving it to timer
