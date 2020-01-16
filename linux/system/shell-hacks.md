# shell hacks

## ffmpeg screen recording
```bash
ffmpeg -y -f x11grab -framerate 30 -video_size WIDTHxHEIGHT -i :0.0+0,0 -c:v libx264 -pix_fmt yuv420p -qp 0 -preset ultrafast NAMEOFVIDEO.avi
```
it's advisable to convert the recorded file  to mkv with

```bash
ffmpeg -i NAMEOFVIDEO.avi NAMEOFVIDEO.mkv
```

## download the whole site
```bash
wget --random-wait -r -p -e robots=off -U mozilla http://www.example.com
```

## download website, 2 levels deep, wait 9 sec per page
`wget --wait=9 --recursive --level=2 http://example.org/`
(you can add user agent if you have any problems, because some sites will check it)

## download all jpg files named cat01.jpg to cat20.jpg
`curl -O http://example.org/xyz/cat[01-20].jpg`
