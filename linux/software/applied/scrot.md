## Copy screenshot into buffer

```bash
scrot ~/scrns/scrn%Y_%m_%d-%H%M%S.png -e 'ls $f | xsel -ib'
```
