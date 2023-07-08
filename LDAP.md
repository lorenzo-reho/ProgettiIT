[LDAP](https://it.wikipedia.org/wiki/Lightweight_Directory_Access_Protocol).
### Che cos'è LDAP?
LDAP (Lightweight Directory Access Protocol) è un protocollo per fare delle interrogazioni a dei servizi di directory. Quindi praticamente questo protocollo viene applicato a dei servizi un cui è presente una base di dati strutturata ad albero, utile per memorizzare informazioni riguardo agli utenti della rete, indirizzi email, credenziali ecc... 
In particolare la struttura dati è costituita da un insieme di entry (nodi dell'albero), le quali hanno una serie associazione attributo-valore, per ogni attributo ci possono essere più valori.
Spesso il protocollo LDAP viene sfruttato negli ambienti windows per implementare servizi di Active Directory, questo perchè fornisce una metodo pratico e sicuro per memorizzare le informazioni riguardo gli utenti che possono accedere alle risorse di rete.

### Funzionamento e panoramica del protocollo

- Esempio di struttura dati:
![[ldap_esempio.png]]
Seguendo la struttura rappresentata nell'immagine, è possibile avere informazioni riguardo l'utente jparker in questo modo:

```
uid=jparker,ou=People,dc=pisoftware,dc=com
```

Tecnicamente questa stringa viene chiamata DN (Distinguished Name) che rappresenta l'entry del set di attributi che si riferiscono all'utente jparker.

In corrispondenza di una entry è possibile fare delle query particolari in cui si esfiltra tutto il contenuto o parte di esso applicando dei filtri o uno ```scope``` di ricerca.

Le possibili operazioni che un utente può chiedere al server sono queste:
-   Bind — esegue l'autenticazione
-   Search — esegue una ricerca
-   Compare — esegue un test di confronto tra un valore e il valore assegnato ad un attributo
-   Add - aggiunge un nuovo oggetto
-   Delete - cancella un oggetto
-   Modify - modifica gli attributi di un oggetto
-   Modify Distinguished Name (DN) — sposta o rinomina un oggetto
-   Abandon — annulla una richiesta inviata in precedenza
-   Extended Operation — richiesta di operazioni estese (definite in altre RFC)
-   Unbind — indica al server di chiudere la connessione (non è esattamente l'inverso della Bind)
-   [StartTLS](https://it.wikipedia.org/wiki/StartTLS "StartTLS") — estensione per utilizzare [Transport Layer Security](https://it.wikipedia.org/wiki/Transport_Layer_Security "Transport Layer Security") (TLS) per eseguire la Bind

### Modalità di interrogazione
[LDAP](https://social.technet.microsoft.com/wiki/contents/articles/5392.active-directory-ldap-syntax-filters.aspx).

Per interrogare un servizio di tipo LDAP è possibile sfruttare una URL formattata in questo modo:

```bash
ldap://host:port/DN?attributes?scope?filter?extensions
```

-   ldap://_ indica lo _scheme_ della URL e identifica il protocollo LDAP
-   _host_ è il [FQDN](https://it.wikipedia.org/w/index.php?title=Fully_qualified_domain_name&action=edit&redlink=1 "Fully qualified domain name (la pagina non esiste)") o l'indirizzo IP del server LDAP.
-   _port_ è la porta sulla quale contattare l'host
-   _DN_ è il nome completo ("distinguished name") utilizzato per identificare la base (il punto di partenza) della ricerca.
-   _attributes_ è la lista (con gli elementi separati da virgole) di attributi da recuperare.
-   _scope_ specifico l'ambito d'azione della ricerca (può essere "base", "one" oppure "sub").
-   _filter_ è il filtro di ricerca (come definito nella [RFC 4515](https://tools.ietf.org/html/rfc4515)).
-   _extensions_ sono estensioni alla richiesta LDAP

In ambienti Linux si sfrutta il software _ldapsearch_ che fornisce una pratica interfaccia testuale utile per connettersi, autenticarsi e fare le varie interrogazioni e modifiche.

- Per esfiltrare tutto il contenuto della entry indentitificata dal DN __"dc=example,dc=local"__:
```bash
ldapsearch -x -D "example/ldap" -W -H ldap://example.local -b "dc=example,dc=local" '(objectClass=*)'
```

- Per esfiltrare solo le entry di tipo user
```bash
ldapsearch -x -D "example/ldap" -W -H ldap://example.local -b "dc=example,dc=local" '(objectClass=user)'
```

- objectClass: identifica il tipo di classe dell'entry da andare a recuperare, è anche possibile utilizzare delle regex per riferirsi a tutte o parti delle classi esistenti.

### Security info:

- Per scannerizzare il servizio di ldap senza essere autenticati: [Nmap scan over ldap](https://www.infosecmatter.com/nmap-nse-library/?nse=ldap-search)
```bash
nmap --script=ldap* <target_ip>
```
Questo comando potrebbe essere in grado di esfiltrare i DN che si riferiscono alle informazioni che riguardano i singoli utenti registrati, o comunque potrebbe fornire un'idea di come le varie entry sono organizzate.


