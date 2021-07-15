# Bar News


Images are stored in `INTERFACE/NEURONET/NEWSVENDOR/newsvendor.txm`

News is described in `DATA/MISSIONS/news.ini`

```ini
[NewsItem]
rank = MIN, MAX
autoselect
;seem to map to .3db icons in NEWSVENDOR
icon = ( critical, faction, system, universe, world ) 
logo = TEXTURE NAME
category = STRING IDS
headline = STRING IDS
text = STRING IDS
base = BASE ID (can be repeated)
```

