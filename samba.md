# SAMBA
## Cos'è Samba?
Samba è un servizio che consente la condivisione di risorse all'interno di una rete, ad esempio:
- Directory
- Stampante
- Scanner 
- ecc...

Samba può essere utilizzato per creare file server centralizzati sia su Windows che su Linux, ma la cosa che rende questo strumento molto potente è che il file server può interagire con dei workgroup di Windows.
In questo modo in azienda si può avere una postazione centralizzata con S.O. Linux Server (che ovviamente essendo open source elimina i costi di licenza) e altre postazioni applicative con S.O Windows.

## Come funziona Samba?
Il servizio Samba si basa sul protocollo SMB/CIFS e sfrutta due demoni, smbd e nmbd, il primo si occupa di gestire le risorse condivise, mentre il secondo ha due funzioni ben precise, gestire la risoluzione dei nomi e la master browser list. 

Come sappiamo, in una rete, tutti i dispositivi sono identificati da un indirizzo IP, ad ogni modo è possibile utilizzare il protocollo WINS per creare una associazione host : ip (simile a ciò che fanno i DNS). WINS e DNS funzionano in modo molto diverso, i server DNS contengono le associazioni riguardante SOLO la rete locale in cui si trovano, e se il dominio non è presente all'interno del server DNS locale, allora si va alla ricerca del corrispondente ip all'interno di altri server DNS. Con i WINS questo non accade perchè tutte le informazioni devono essere contenute all'interno di un unico file, ma ha il vantaggio di essere aggiornato automaticamente, mentre questo non accade con i DNS.

La Master Browser List è invece una lista di dispositivi connessi e visibili in rete in un determinato momento che si trova su una macchina scelta automaticamente sulla base di alcuni criteri, come ad esempio il tempo di attività o se una macchina è Server oppure no. In realtà non viene fatta alcuna scansione della rete ma semplicemente le macchine che vengono accese si preoccupano si comunicare la priopria presenza al Server che tiene la MBL.

Quando si fa accesso ad una directory condivisa smb, si hanno i permessi dell'utente con cui si è fatto l'accesso.

## File di configurazione:
Il file di configurazione di samba è /etc/samba/smb.conf ed è strutturato in questo modo:

```bash
\[global\]
workgroup = GRUPPODILAVORO # nome del workgroup in cui sono inserite le macchine Windows
netbios name = SERVER1 # nome del file server linux con cui viene visto dai client quando
security = USER # l'autenticazione avviene tramite userid e password
encrypt passwords = YES # Abilita la crittografia delle password
server min protocol = SMB2_10 # forziamo l'utilizzo di un protocollo SMB2+ per il server
client min protocol = SMB2 # forziamo l'utilizzo di un protocollo SMB2+ per il client
client max protocol = SMB3

\[gestionale\]
comment = Gestionale # commento che viene visualizzato quando vengono listate le risorse di rete condivise
path = /home/gestionale # path della directory da condividere
valid users = @amministrazione # possono accedere gli utenti che appartengono al gruppo amministrazione
writable = YES
force group = amministrazione # quando un utente crea un file nella dir, questo appartiene al gruppo amministrazione
create mask = 0660 # quando il file viene creato, ha questa maschera dei permessi

\[homes\] 
path = /home/%U # le partizioni di home devono essere prese dalla directory /home, con %U si intende lo userid dell'utente client che sta accedendo alla directory
writable = YES
browsable = NO # Non viene visualizzata la directory homes
valid users = %S # con %S si intente il nome della directory condivisa

... qui si possono inserire altri blocchi ...

## comandi base di Samba

```

## Comandi Fondamentali:

Per installare samba:

```bash
sudo apt install samba smbclient
```

Per far partire il servizio:

```bash
systemctl start smbd
systemctl start nmbd
```

Per stoppare il servizio:

```bash
systemctl stop smbd
systemctl stop nmbd
```

Per controllare lo stato del servizio:

```bash
systemctl status smbd
systemctl status nmbd
```

Per controllare la sintassi del file di configurazione:

```bash
testparm
```

Per controllare anche i parametri impostati automaticamente:

```bash
testparm -v
```

Per listare le risorse condivise attive:

```bash
smbclient -L <NetBiosName>
```

Per settare la password degli utenti che possono accedere alla dir:

```bash
sudo smbpasswd -a <Nome User>
```

### LINK UTILI: