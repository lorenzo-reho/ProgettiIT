### Binaries Path:
- /bin: Contiene quei binari che possono essere fatti partire prima del mount di /usr, per questo motivo contiene binari di sistema utili nella fase di boot.
- /sbin: Uguale al primo ma per binari eseguibili come super users.
- /usr/bin: Binary eseguibili dopo che /usr viene montata.
- /usr/sbin: Uguale al precedente ma per binari eseguibili tramite root.

### Ottenere una shell root durante il boot del sistema

1)  Riavvia il computer
2)  Seleziona l'immagine di avvio principale
3)  premere 'e' per andare in modalità modifica
4) individuare la string che inizia con linux
5) andare alla fine e modificare 'ro' in 'rw' e aggiungere init=/bin/bash
6) F10 per terminare il BOOT

### Configurazione delle interfacce di rete (TODO)
Il file di configurazione delle interfacce di rete è presente all'interno della directory /etc/netplan

### PERMESSI
I file all'interno del file system di un sistema linux sono caratterizzati da una serie di permessi (R Read, W Write, X Execution), per ogni file è possibile settare dei permessi per l'utente che possiede il file, il gruppo a cui appartiene e per tutti gli altri utenti.

![[esempio_permessi.PNG]]
Il primo bit serve a discriminare se si tratta di un file o di una directory, i successivi 3 bit corrispondono ai permessi dell'utente che possiede il file, i successivi 3 bit corrispondono ai permessi per gli utenti che fanno parte del gruppo a cui il file appartiene, gli ultimi 3 bit fanno riferimento ai permessi di tutti gli altri utenti che non ricadono nelle prime due categorie.

Il primo bit di ogni tripla corrisponde al permesso di lettura (R)
Il secondo bit di ogni tripla corrisponde al permesso di scrittura (W)
Il terzo bit di ogni tripla corrisponde al permesso di esecuzione (X)

Impostazione dei permessi:

```bash
chmod 661 <file path>
chmod u+r <file path> <-- aggiunge il permesso di lettura dallo user
chmod u+w <file path> <-- aggiunge il permesso di scrittura dallo user
chmod u+x <file path> <-- aggiunge il permesso di esecuzione dallo user
chmod u-r <file path> <-- rimuove il permesso di lettura dallo user
```

### SUID

I file all'interno del file system di un sistema linux, oltre ai bit che riguardano i permessi, possono anche avere un'altro tipo di bit settato, chiamato bit SUID, quando questo bit è attivo tra i permessi dell'utente allora si può eseguire il file con i permessi dell'utente che lo possiede.

![[esempio_suid.PNG]]
In questo esempio notiamo che è attivo l'SUID sul bit di Execute dello user, in questo modo chiunque può eseguire quello script con i permessi dell'utente che lo possiede, in questo caso "lorenzo". Le possibili implicazioni che riguardano la sicurezza possono essere molteplici, immaginiamo che script.sh sia posseduto da root, potrebbe essere un punto interessante per eseguire una privilege escalation.

Per settare l'suid ad un file sulla tripla di bit corrispondente allo user:
```bash
chmod 4766 script.sh
```

Per visualizzare tutti i file con suid attivo:
```bash
find / -perm -u=s -type f 2>/dev/null.
```

### PROCESSI
[Processi](https://rigadicomando.it/tutto-quello-che-devi-sapere-su-ps).
[Understanding processes](https://devconnected.com/understanding-processes-on-linux/#:~:text=what%20processes%20are.-,Linux%20Processes%20Basics,a%20web%20server%20for%20example.&text=On%20the%20other%20hand%2C%20programs,on%20a%20persistent%20data%20storage.).
In generale un processo è semplicemente un'istanza di un programma all'interno della memoria RAM, come sappiamo i programmi si trovano all'interno del disco fisso e sono formati da dati e istruzioni, quando il programma diventa processo allora viene allocato in memoria uno spazio riservato ad esso e vengono associate altre aree di memoria per lo stack, l'heap ecc...

Su linux, come anche in altri sistemi operativi, i processi sono gestiti dal kernel, in particolare esiste un processo con PID 1 generato dal kernel (quindi il proprietario è root) che ha il compito di eseguire i primi comandi di base.

Ogni processo mantiene una serie di informazioni, tra cui:
- Utente che ha generato il processo.
- PID del processo.
- Comando che lo ha generato

Per visualizzare tutti i processi posseduti dall'utente corrente:
```bash
ps
```

Per visualizzare tutti i processi attualmente attivi nel formato esteso:
```bash
ps -ef
```


### COMANDI (GENERALI) FONDAMENTALI:

Aggiungi un utente:

```bash
sudo useradd <username>
sudo passwd <username>
```

Imposta un utente come sudoer:

```bash
sudo usermod -a -G sudo <username>
```

Creazione di un nuovo gruppo:

```bash
sudo groupadd <groupName>
```

Aggiungi un utente ad un gruppo:

```bash
sudo usermod -a -G <groupName> <username>
```

Visualizzare tutte le shell disponibili:

```bash
cat /etc/shells
```

Split string sulla base di un carattere:

```bash
cat -d ";" -f 1
```

Eliminare spazi bianchi all'inizio e alla fine:

```bash
cat file | xargs
```

Cercare un file nel file system (Si possono sfruttare le regex)

```bash
find <path> -name <file>
```

Visualizzare tutte le porte in ascolto:

```bash
netstat -a
```

Visualizzare quali processi sono attivi su ogni porta in ascolto:

```bash
netstat -ltnp | grep -w ':<port>'
```

Gestione dei gruppi e permessi per i web services:

```bash
chown -R lorenzo hackpills.it
chgrp -R www-data hackpills.it
chmod -R 750 hackpills.it
chmod g+s hackpills.it
```

Per visualizzare se ci sono script/binari che possono essere eseguiti con i privilegi di root senza la necessità di autenticarsi tramite password:

```bash
sudo -l
```

Settare una variabile d'ambiente quando si esegue uno script, la modifica non verrà mantenuta dopo la terminazione del processo:

```bash
PATH=/tmp ./script.sh
```

Per generare dei certificati tramite openssl
```bash
openssl req -newkey rsa:2048 -new -nodes -x509 -days 3650 -keyout key.pem -out cert.pem
```
+
Per killare un processo in ascolto su una determinata porta
```bash
fuser -k <port>/tcp
```