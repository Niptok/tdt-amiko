

## build known issues ##
  * if you have an error like this with libusb compilation
```
jade:E: specification document does not have the DSSSL architecture as a base architecture
jade:E: no style-specification or external-specification with ID "HTML"
```
> the easiest solution is to remove **_jade_** and not try to build these docs
  * An issue with python if you are sure that you have 2.7 but see this
```
This version of the AC_PYTHON_DEVEL macro
doesn't work properly with versions of Python before
2.1.0. You may need to re-run configure, setting the
variables PYTHON_CPPFLAGS, PYTHON_LDFLAGS, PYTHON_SITE_PKG,
PYTHON_EXTRA_LIBS and PYTHON_EXTRA_LDFLAGS by hand.
Moreover, to disable this check, set PYTHON_NOVERSIONCHECK
to something else than an empty string.
```
> the solution is **`export TERM=linux`**
  * An issue with newer lua 5.2 lib
```
 rpm: symbol lookup error: /usr/lib/librpmio.so.2: undefined symbol: lua_call
```
> You need lua 5.1 library. It could be installed to an optional dir, and loaded like this **` export LD_PRELOAD=/usr/lib/liblua.so.5.1`**

  * An issue with **rpm > 4.9**
```
Error: /home/user/tdt-amiko/tdt/cvs/cdk/SPECS/stm-cross-gcc.spec: 11: Found
%endif without %if
```
> See https://bugzilla.stlinux.com/show_bug.cgi?id=23138 and downgrade to rpm-4.9.x

  * libtool problems
> Getting crazy of libtool linking errors? This article help you to keep things in order http://www.metastatic.org/text/libtool.html

  * gcc **> 4.7** segfault
> please downgrade.

  * texinfo **> 4.13**
> to build binutils you need temporary install v4.13


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
Тулчейн качает по read-only url а мы хотим сразу пушить, то пишем в  ~/.gitconfig
```
[url "git@github.com:technic/amiko-e2-pli.git"]
	insteadOf = git://github.com/technic/amiko-e2-pli.git
```

Use ssh agent to store password for git rsa keys. The following command allows to enter password once.
```
ssh-add
```
Start agent if it is down
```
eval `ssh-agent`
```

Interesting application to learn git online
http://pcottle.github.com/learnGitBranching/

add these lines to ~/.gitconfig and use git wdiff to see changes by words
```
[alias]
        wdiff = diff --word-diff-regex='[^[:space:]]|([[:alnum:]]|UTF_8_GUARD)+'
```

Qgit is aware of some git log flags, you may find sort by date like on github usefull.
```
qgit --date-order --all
```

## make tips ##
If you see strange make behaviour, you can find some useful information with **`make --debug`** and **`make --warn-undefined-variables`**

Consider how make interacts with enviroment variables. Misunderstanding of these rules could lead to error. So my test Makefile is the following
```
A = make
B ?= make

all:
	@echo A=$(A)
	@echo B=$(B)
	@echo C=$(C)
```
firstly we don't set any variables from console
```
$ make -f q.mk 
A=make
B=make
C=
```
Variables from environment replace **`?=`** assigments and **undefined** variables.
```
$ A=env B=env C=env make 
A=make
B=env
C=env
```
Variables from make arguments **replace all** Makefile vars !
```
$ make A=arg B=arg C=arg
A=arg
B=arg
C=arg
```



### logging ###
to save log output of some\_command run
```
some_command 2>&1 | tee all.log
```

### quilt ###
editing patches
```
cd package-build-dir
quilt import -p1 ../Patches/your-fix.patch
quilt push -a
# edit files
quilt refresh
cp patches/your-fix.patch ../Patches
```
See man quilt for more info

### grep highlight ###
highlight regexp in output of some command with grep
```
cmd | grep -e 'regexp' -e $
```
Want multicoloured ?
```
alias grep='grep --color=always'
alias yellow-grep='GREP_COLOR='\''1;33'\'' grep --color=always'
alias highlight='grep -e error -e $ |yellow-grep -e warning -e $'
```
thanks to http://www.jefftk.com/news/2009-01-19


### gcc verbose ###
It might be helpful to dump gcc search paths, remember gcc `-v` flag

### Makefile hacks ###
How-to echo multiline variable or execute multiline script ?
```
define mylines
line 1
line 2
line 3
endef
export mylines
# now mylines is enviroment variable accessible by bash!
writelines:
	echo "$${mylines}"
```
yes, you could exec some other scripting staff, such as python
```
define mypy
for x in [1,2,3,4]:
    print bin(i)
endef
export mypy
execscript:
	python -c "$${mypy}"
# or
        printenv mypy | python
```

_NOTE:_ on my ubuntu system maximum export variable size is **64K**

more: [link](http://stackoverflow.com/questions/649246/is-it-possible-to-create-a-multi-line-string-variable-in-a-makefile)

## Logging ##
### COM port log ###
How to see debug info with com port is described in this thread ( russian )
http://www.allrussian.info/index.php?page=Thread&threadID=141918
Google for instructions in other languages.
### writing to com port doesn't work ###
For some devices changing minicom options helps. This is how it works for me:
```
tech@dell1535:~$ cat /etc/minirc.dfl 
# Machine-generated file - use "minicom -s" to change parameters.
pu port             /dev/ttyUSB0
pu rtscts           No
```
_Hardware Flow Control_ is NO

### listen syslogd over udp ###
This is a python script to listen on udp port 9514. Default 514 port requires root access. You need to set your PC ip in the syslog config on the box. Blank UDP\_IP allows to listen on all interfaces.
```
import socket
import re

UDP_IP = ""
UDP_PORT = 9514

sock = socket.socket(socket.AF_INET, # Internet
                     socket.SOCK_DGRAM) # UDP
sock.bind((UDP_IP, UDP_PORT))

while True:
    data, addr = sock.recvfrom(1024) # buffer size is 1024 bytes
    print re.sub('<\d+>', '', data) ,
```

Syslog has log file in /tmp/messages too.

### Add timing to any output ###
```
some-command | awk '{ print strftime("%s"), $0; }'
```

## Git work flow conventions ##

  * есть опенпли оригинал **upstream >>**
  * из него мержим в **openpli** - это почти клон оригинала, но сюда pick-аем фиксы которы хотим отправить в **>> upstream**. Ничего связанного с sh4 и другими нашими фичами тут нету. **>>**
  * Из  него идем в **staging** - все коммиты в этом бранче будут относиться к мержу из **openpli**, исправлениям связанным с ошибками при мерже, но ничего другого. **>>**
  * И наконец доходим до **master** cюда после проверки, по возможности ;), мержим staging. Код в master пусть находится в более менее рабочем состоянии. Есть идея давать людям регулярно обновляться через opkg. Делаем коммиты сюда и следим друг за другом. **>>**
  * **<< featureX** Можно отвлетвляться в небольшие временные веточки типа httpstream..

В обратную сторону..
  * (staging) заберает всё с <- (master)
  * (openpli) заберает багфиксы с <- (master)
  * (upstream)-у отправляем патчи на форум

## Git sources & commit format ##
Make sure that

  * You don't break indentation. Don't mix different types of indentation, leave them in the original state, because we want to merge upstream from time to time. For example there are TABs in enigma2.
  * You use unix line ending.
  * There is nothing like "No new line at the end of file" reported by `git diff`.
  * `git diff` doesn't report any trash.

  * Usually, first one or two words in the commit message describe component you make change to. Changes description comes next.
  * Don't abuse the words "fix", "optimize" if there is no fix/optimize. No yellow press headlines please.
  * More description in a commit is highly appreciated.

  * Don't worry, we are working 4fun :)

## Issue reporting ##
  * We understand Russian, English and German
  * Мы понимаем, Русский, Английский и Немецкий

### check for duplicates ###
Select search -> All issues and look through them too.

### fast reply ###
I've found that google-code supports replying to issues by email.

## a bit IDE ##
For example, you can open enigma2 in kdevelop as Makefile project to use auto completion and syntax highlight and symbols browser.

Moreover if you want to see compiler errors and warning in a bit more comfortable way set environment variables and build with kdevelop too.

Unfortunately I haven't got any guide how to profile and debug yet.

## Как я мержу с енигмой от Макса которая идёт из патчей ##
```
 //удалить если осталась эта ветка с прошлого раза
gb -d tmp
 //git status. Выкинуть все untracked файлы.
gst
 //Только чтобы были файлы из гита.
rm ... rm ... rm ...
 //перешли в коммит поверх которого накладывать патчи
gco <hash> -b tmp
 //наложили нужный патч
git apply /home/.../tdt/cvs/cdk/Patches/enigma2-nightly.X.diff
 //добавили все изменения включая новые файлы
ga .
 //имя коммита я делаю 'patch on top of <первые семь знаков хеша>'
gcm
 //заменили всё что было в patched на то что в tmp. Это не мерж а по сути замена.
git merge patched -s ours
 // переключили на patched
gco patched
 // просто fast-forward.
git merge tmp
```