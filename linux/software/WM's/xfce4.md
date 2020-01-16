# xfce4

## change hotkeys of terminal
`~/.config/xfce4/terminal/accels.scm` just edit hotkey and remove leading **;**

## disable window roll-up on mouse wheel

    1. go to settings> setting editor
    2. click on xfwm4 in 'chanel side bar
    3. click on general to display tree list and find one called  `mouesewheel_rollup`
    4. click on to highlight and click edit icon at top of window
    5. its a Bool so all you need to do is uncheck enable box.
    6. save

## open folder in firefox or transmission opens catfish (or doesn't work at all)
1. Change the default app for handling dirs
`xdg-mime default Thunar-folder-handler.desktop inode/directory`
2. Refresh MIME DB and APP DB (as NORMAL user)

## lock screen before suspend
`session and startup -> advanced -> lock screen before sleep`

## save open dialog make directories first
right click -> sort folders before files
