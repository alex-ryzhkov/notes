# vim

## vim use default settings when sudo
use either `sudo -E vim <file>` or `sudoedit vim <file>`
but with the second variant you have to set filetype manually

## open files in terminal vim instead of gvim
 I edited `/usr/share/applications/vim.desktop` and changed the value for `Exec`
 adding `Exec=xfce4-terminal -e "vim %F"` and, *IMPORTANT* set `Terminal=false`

## swap escape and caps lock
put `setxkbmap -option caps:swapescape` in `$HOME/.profile`

## powerline
    1. configure separators in vim-airline
    2. you need to install powerline fonts: https://github.com/powerline/fonts
    3. don't forget to set a font (Deja vu sans mono 12) in your terminal
    4. also see :help airline-customization
[Source](https://vi.stackexchange.com/questions/3359/how-do-i-fix-the-status-bar-symbols-in-the-airline-plugin)

## vim and cyrillic alphabet
[Vim и кириллица: парочка приёмов / Habr](https://habr.com/post/98393/)

