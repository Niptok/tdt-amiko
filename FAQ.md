## enable telnet password ##
It is not user friendly at the moment:
  1. in `/etc/init.d/telnetd` remove
```
-- -l /bin/autologin
```
  1. run `passwd` and set new password
  1. reboot