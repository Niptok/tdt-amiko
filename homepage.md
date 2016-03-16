# AR-Project home #

### new re-factored build system ###
https://github.com/OpenAR-P/arp-bs

### new sources ###
22.07.2014 see our git hub team :)
It looks like we are migrating to github...

### we have github team ###
Check it [here](https://github.com/OpenAR-P)

### new Image release ###
20.01.2014

### monitor upstream <a href='http://dev.duckbox.info/cgi-bin/cgit.cgi'>git</a> ###

## New sources pushed to git ##
update on 6.05.2013

## update feed launched ##
<a href='http://code.google.com/p/tdt-amiko/wiki/updates'>log</a>

replace /etc/opkg/official-feed.conf with this 2 lines
#### alien2 ####
```
src/gz box http://schpuntik.dyndns.org/feed/alien2/ipkbox 
src/gz extras http://schpuntik.dyndns.org/feed/alien2/ipkextras 
```
#### alien1 ####
```
src/gz box http://schpuntik.dyndns.org/feed/alien1/ipkbox 
src/gz extras http://schpuntik.dyndns.org/feed/alien1/ipkextras 
```
## introduce build system ##
<a href='http://wiki.tdt-amiko.googlecode.com/git-history/master/build.html'>small guide</a>

## track issue updates ##
<a href='https://groups.google.com/forum/m/?fromgroups#!forum/tdt-amiko'>by google groups</a>

## Upstream issues ##
<a href='https://bitbucket.org/duckbox/sh4-duckbox-project/issues'>link</a>

## git tips ##
чтобы сделать историю менее ветвистой иногда имеет смысл пользоваться
```
git pull --rebase
```
вместо
```
git pull
```
возможно понадобиться временно положить локальные изменения в карман
а потом их обратно применить
```
git stash
git stash pop
```