### 1. Autonomous System Recon

[Hurricane Electric Internet Service](https://bgp.he.net/)

#### 1.1 Cosa sono i sistemi autonomi?
I sistemi autonomi sono delle grandi rete che si estendono su una vasta area. Ogni sistema autonomo ha delle politiche di routing prestabilite che valgono in tutto il sistema. Fungono da ufficio postale che riceve i pacchetti e li invia presso il corretto sistema autonomo che contiene l'host o la rete di destinazione.

#### 1.2 Come si identificano i sistemi autonomi?
I sistemi autonomi vengono identificati da un numero __ASN__.

#### 1.3 Enumerare i domini all'interno di un Autonomous System

```bash
amass intel -asn 13374,14618
```

### 2. Servizi di terze parti
[BuildWith](https://builtwith.com/)

### 3. Enumerare subdomains

#### 3.1 Amass
Amass consente di enumerare i domini attingendo a delle lunghe liste di sottodomini (alcuni non ancora in vita) immagazzinate da grossi servizi (Virustotal, Github ecc...)

```bash
amass enum -d openai.com
```

#### 3.2 Subfinder
Subfinder consente di fare bruteforce del subdomain e integrare (tramite API Keys) la ricerca presso altri servizi.

```bash
subfinder -d openai.com
```

#### 3.3 Assetfinder
```bash
assetfinder openai.com
```

#### 3.4 Waybackurl
Estrarre tutti i vecchi url per ogni dominio che sono stati salvati in un database pubblico online

```bash
cat domains.txt | waybackurls > urls
```

#### 3.5 Dnmasscan
Scan delle porte a partire da una lista di domini

```bash
dnmasscan domains.txt dns.log -p 80,443 -oG masscan.log 
```


### 4. Analisi HTTPX URLs

#### 4.1 HTTProbe
Controllare quali domini sono attivi e raggiungibili tramite protocollo HTTP/S

```shell
cat domains.txt | httprobe
```

#### 4.2 Httpx
Utile per controllare qual è la risposta ad una semplice richiesta GET HTTP/S ad un set di url.

```bash
httpx -l httpx_urls.txt -sc -title -probe -ip
```

- __sc__: Status Code
- __title__: Title della pagina
- __probe__: Display Probe status
- __ip__: IP risolto

#### 4.3 FFF
Estrazione header e body da un set di URL tramite richieste GET

```bash
cat urls.txt | fff -S -o out_folder
```

#### 4.4 Eyewitness
Ottenere uno screenshot per ogni pagina o url contenuto in un file

```shell
EyeWitness -f urls.txt --web
EyeWitness -x urls.xml --timeout 8 
EyeWitness -f urls.txt --web --proxy-ip 127.0.0.1 --proxy-port 8080 --proxy-type socks5 --timeout 120
```

Potrebbe essere utile passare attraverso il proxy di BurpSuite per ottenere una sitemap più completa e eseguire degli scanner più approfonditi (Spider e Crawl). 

#### 4.5 Subdominizer

-   To list help about the tool:

```bash
python3 SubDomainizer.py -h
```

-   To find subdomains, s3 buckets, and cloudfront URL's for given single URL:

```bash
python3 SubDomainizer.py -u http://www.example.com
```

-   To find subdomains from given list of URL (file given):

```bash
python3 SubDomainizer.py -l list.txt
```

-   To save the results in (output.txt) file:

```bash
python3 SubDomainizer.py -u https://www.example.com -o output.txt
```

-   To give cookies:

```bash
python3 SubDomainizer.py -u https://www.example.com -c "test=1; test=2"
```

-   To scan via github:

```bash
python3 SubDomainizer.py -u https://www.example.com -o output.txt -gt <github_token> -g 
```

-   No SSL Certificate Verification:

```bash
python3 SubDomainizer.py -u https://www.example.com -o output.txt -gt <github_token> -g  -k
```

-   Folder Scanning:

```bash
python3 SubDomainizer.py -f /path/to/root/folder/having/files/and/folders/  -d example.com  -gt <github_token> -g  -k
```

-   Subject Alternative Names:

```bash
python3 SubDomainizer.py -u https://www.example -san all
```

-   Saving secrets to a file scan found in github:

```bash
python3 SubDomainizer.py -u https://www.example.com -o output.txt -gt <github_token> -g -gop filename_to_save
```