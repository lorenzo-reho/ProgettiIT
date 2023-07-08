# NGINX

## Che cos'è NGINX?
Nginx è un Reverse Proxy, WebServer e Load Balancer, in grado di supportare numerosi protocolli diversi come ad esempio: HTTP(S), WebSocket, IMAP, POP3, SMTP

Come Reverse Proxy, NGINX è in grado di ascoltare delle richieste da parte di uno o più client e decidedere quale applicazione servire. NGINX effettua la scelta di servire una applicazione al posto di un'altra sulla base dell'URL.

NGINX è un web server asincrono, questa proprietà rende più efficiente la gestione di più client che fanno delle richieste e attendono delle risposte con un delay tendente a zero.

## Path e file fondamentali:
- /etc/nginx/nginx.conf: Questo è il file di configurazione entry-point, il primo ad essere eseguito, qui dentro sono presenti tutte le direttive principali che nginx dovrà seguire per garantire i servizi.
- /etc/nginx/conf.d: è una directory in cui si possono creare altri file di configurazione secondari utilizzabili poi all'interno di quello primario.
- /var/log/nginx/access.log: è un log file in cui vengono registrate le richieste fatte dai client (sono presenti anche delle infomazioni sull'ambiente utilizzato dal client per fare la richiesta)
- /usr/share/html: è una directory in cui sono presenti tutti i file che compongono la web application.

## Master Process e Worker Process
TODO

## UpStream e Load Balancing
Nginx possiede una serie di strumenti che consentono di applicare delle politiche di bilancio del carico tra più applicazioni diverse, in particolare è possibile avviare un server in ascolto su una determinata porta e in corrispondenza di una location, servire una applicazione differente. Spesso però quando le richieste sono molte, potrebbe essere necessario distribuire il carico di una applicazione su più server, lo strumento che Nginx ci mette a disposizione per gestire questa evenienza è upstream.

```nginx
upstream www_service_com{
	server 127.0.0.1:8080;
	server 127.0.0.2:8080;
	server 127.0.0.3:8080;
}

server {
	listen 80;
	location / {
		...
	}
	location  /service {
			proxy_pass http://www_service_com;
	}

	location /store {
		...
	}
}
```

Quando si effettua un proxy_pass è possibile impostare una uri di base per tutte le risorse statiche che vengono richieste direttamente dall'applicazione:

```nginx
	 location /service/ {
			proxy_pass http://application/; #trailing / is important!

			sub_filter 'action="/'  'action="/grav/';
			sub_filter 'href="/'  'href="/grav/';
			sub_filter 'src="/'  'src="/grav/';
			sub_filter_once off;

	}
```

Se gzip dell'upstream è on, allora è necessario aggiungere questo header all'interno di location:

```nginx
proxy_set_header Accept-Encoding ""; # Questa riga rimuove la compressione della risposta
```

Fino ad ottenere questo:

```nginx
	 location /service/ {
			proxy_set_header Accept-Encoding "";
			proxy_pass http://application/; #trailing / is important!
			sub_filter 'action="/'  'action="/service/';
			sub_filter 'href="/'  'href="/service/';
			sub_filter 'src="/'  'src="/service/';
			sub_filter_once off;

	}
```

Quando si effettua un proxy_pass, potrebbe essere necessario settare il campo Host all'interno dell'header della richiesta effettuata dal proxy, spesso si presenta questa necessità quando l'applicazione finale controlla l'host utilizzato per capire se è all'interno di una whitelist. Esempio:

```nginx
	location /service/ {
		proxy_pass http://application/;
		proxy_set_header Host <host>;
	}
```

## Rewrite
A volte quando si richiedono determinate risorse potrebbe essere necessario modificare parte dell'URL (come ad esempio il base path) e rifare la richiesta. Ad esempio se l'applicazione si trova sotto http://www.example.it/app1 ma poi il traffico viene inviato su un servizio in ascolto sulla porta 8080, allora potrebbe presentarsi la necessità di "riscrivere" l'URL delle richieste (fatte dall' applicazione presente sulla porta 8080) che si riferiscono a delle risorse statiche, aggiungendo /application e agevolando di conseguenza il trigger del reverse proxy che si interpone tra applicazione finale e client.

Ad esempio:

```nginx
location / {
    rewrite ^((?!/app1).*) /app1$1 permanent;
    ...
}
```

Questo blocco di codice controlla se l'url è tipo http://www.example.it/asd/risorse/ e quindi non ha /app1 subito dopo il dominio, allora l'URL viene modificato in questo modo http://www.example.it/app1/asd/risorse/ e si rigenera la richiesta con l'url aggiornato, in pratica avviene un redirect.

Alla fine del rewrite si può aggiungere un:
- permanent (301 Moved Permanently): Per rendere permanente il redirect e quindi far apparire sulla barra di ricerca il nuovo URL
- redirect (302 Found): Per rendere temporaneo il redirect e quindi effettuare un riferimento ad un altro URL senza però mostrarlo direttamente sulla barra di ricerca. Potrebbe essere necessario durante i periodi di manutenzione.

Ad ogni modo lo stesso obiettivo di riscrittura dell'URL può essere raggiunto utilizzando la direttiva return, in questo modo:

```nginx
location = /app1 {
	return 301 http://$host:8081/app2;
}
```

## FastCGI
[Info PHP CGI e PHP FastCGI](https://docs.rockylinux.org/it/guides/web/php/).

FastCGI server sono dei server utilizzati per gestire una grossa quantità di richieste senza però generare un processo per ognuna di esse. FastCGI supporta diversi tipi di tecnologie come ad esempio: PHP, JAVA, python ecc...

Se si tratta di web applications scritte in php allora è necessario utilizzare un server php-fpm (fastcgi process manager) che funge da proxy per inviare le richieste ad una web application php, php-fpm può essere configurato come servizio http o semplicemente come socket, in questo esempio ho optato per la soluzione http sulla porta 9000. Quindi praticamente le richieste di pagine php che vengono fatte sulla porta 8080, vengono redirezionate sulla porta 9000 del localhost per raggiungere il server php-fpm e poi sarà lui ad inviarle alla giusta web app e farci ritornare le risposta.


```nginx
server{
	listen 8080;
	server_name example.it;
	root /var/www/html/site;
	index index.php;

	location ~ \.php$ {
			include fastcgi_params;
			fastcgi_pass 127.0.0.1:9000;
			fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
			fastcgi_index index.php;
	}
}
```

## Location Block

[Location Blocks](https://www.digitalocean.com/community/tutorials/understanding-nginx-server-and-location-block-selection-algorithms).

Il blocco location contenuto all'interno del blocco server è necessario per decidere che risorsa servire o in generale quale operazione eseguire sulla base dell'URI specificato.

La seguente location verrà eseguita per uri tipo /site o /site/page/index.html:

```nginx
location /site {
	...
}
```

La seguente location verrà eseguita SOLO per uri tipo /site

```nginx
location = /site {
	...
}
```

Per regex case-sensitive:

```nginx
location ~ \.php$ {
	...
}
```

Per regex case-insensitive:

```nginx
location ~* \.php$ {
	...
}
```

### | try_files:
```nginx
try_files $uri $uri/ /index.php;
```

Quando si richiede una risorsa tramite URI, prima di tutto si va a cercare (a partire dalla root directory) se esiste sotto forma di file ($uri), se non esiste allora si passa a cercare se magari è presente sotto forma di directory (\$uri/), se anche questo caso fallisce allora avviene un redirect verso il terzo parametro (in questo caso si va ad index.php).

Se esistesse una location di questo tipo:

```nginx
location @error {
	...
}
```

allora si potrebbe fare:

```nginx
try_files $uri $uri/ @error;
```

## File di configurazione:
[Guida al file di configurazione](https://www.plesk.com/blog/various/nginx-configuration-guide/#:~:text=Every%20NGINX%20configuration%20file%20will,interchangeably%20as%20blocks%20or%20contexts%20.).

Semplice esempio di file di configurazione:

```bash
user www-data; #Il worker viene fatto partire sotto questo utente
worker_processes auto; #Il numero di processi worker è automatico
pid /run/nginx.pid; #Il pid del master è all'interno di quel file
include /etc/nginx/modules-enabled/*.conf; #Inclusione dei moduli utilizzabili

events {
        worker_connections 768; # massimo numero di connessioni per ogni worker
        # multi_accept on;
}

# In questo blocco vengono inserite le informazioni globali riguardo
# ai web server
http {

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3;
        ssl_prefer_server_ciphers on;

        access_log /var/log/nginx/access.log; # Directory dove loggare le richieste
        error_log /var/log/nginx/error.log; # Directory dove loggare gli errori
		
		# configurazione di un singolo Server
		server{
			listen 8080;
			root /var/www/html; # Directory entry
			point del sito web
			index index.html;
		}

        include /etc/nginx/conf.d/*.conf; # Inclusione di tutti i file di conf
        include /etc/nginx/sites-enabled/*;
}


```


## COMANDI FONDAMENTALI:

- Start/Stop/Restart/Status

```bash
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
```

- Remove nginx

```bash
sudo apt remove nginx
sudo apt purge nginx nginx-common
```

- Installa Nginx

```bash
sudo apt update
sudo apt install nginx
```

## LINK UTILI:
