- Scan delle porte
```bash
sudo nmap -sC -sV -oA nmap/scan 192.168.1.60
```

- Scan completo delle porte
```bash
sudo nmap -sC -sV -p- -oA nmap/full_scan 192.168.1.60
```

- Scan degli host all'interno di una rete
```bash
sudo nmap -sL 192.168.1.0/24
```
