
- Recuperare tutti i nomi dei Mail Server
```bash
host -t mx megacorpone.com 
```

- Recuperare tutti i nomi dei Name Server
```bash
host -t ns megacorpone.com 
```

- Controllare se un Name Server esiste
```bash
host www.megacorpone.com 
```

- Per effettuare un zone-transfer per ogni nameserver all'interno del dominio (megacorpone.com)
```bash
dnsrecon -d megacorpone.com -t axfr
```

- Per enumerare tutti i Record (R), Name Server (NS), Mail Server (MX) ... all'interno di un dominio,
```bash
dnsenum zonetransfer.me
```


