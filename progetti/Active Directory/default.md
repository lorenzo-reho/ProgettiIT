
### Cos'è Active Directory?
Active Directory è una tecnologia che si basa sui concetti di dominio e di directory e fornisce una serie di strumenti utili per gestire in modo centralizzato gli utenti e le macchine contenute all'interno di una rete. In questo modo è possibile abilitare alcuni utenti all'accesso di specifiche risorse condivise o dispositivi hardware, organizzare gli utenti in modo logico sulla base dei ruoli che ricoprono, aggiornare, installare software ed eseguire script su ogni macchina appartenente al dominio, onguna di queste operazioni può essere eseguita direttamente dalla macchina server centrale senza dover andare su ogni postazione ed applicare le medesime modifiche. 
Un dominio Active Directory è più sicuro rispetto ad una architettura di tipo Peer-To-Peer perchè esiste un unico server centralizzato su cui risiede il Domain Controller ed ogni utente che cerca di accedere alle risorse di rete deve prima di tutto autenticarsi al dominio.

![ad](imgs/ad.PNG)

### Cos'è un dominio?
Un dominio è un insieme di utenti, dispositivi e risorse contenuti all'interno di una rete e raggruppati sotto un nome comune di dominio (Domain Name), solitamente è gestito da un solo team di amministratori.  

Ad esempio un Domain Name corrispondente ad una piccola realtà aziendale potrebbe essere _company.local_, per riferirsi ai singoli host del dominio si può utilizzare la notazione _host.company.local_ ricordando la struttura gerarchica delle parti che compongono un dominio.

I servizi AD prevedono anche la presenza di un server DNS in grado di risolvere i Domain Name degli host nei corrispondenti indirizzi IP.

### Cos'è un Directory Service Database?
Un Directory Service Database è inteso come un archivio organizzato in modo gerarchico che contiene informazioni riguardo gli utenti e le macchine presenti all'interno del dominio.

Ad esempio per quanto riguarda i singoli _users_ vengono solitamente salvati questi dati:
- __Username__: Nome identificativo dell'utente.
- __UserId__: Identificatore numerico univoco dell'utente.
- __Mail__: Email dell'utente
- __Role__: Ruolo dell'utente all'interno del dominio.
- __Credentials__: Credenziali di accesso al dominio.

Spesso i servizi AD si appoggiano al protocollo LDAP per interagire con una LDAP directory (tipo di Directory Service Database).
In sostanza è un protocollo che consente agli utenti di autenticarsi e fare delle query di ricerca e modifica delle informazioni riguardo altri utenti o risorse presenti nel dominio AD.

### Cos'è un Domain Controller?
Quando si implementa un dominio Active Directory in di una rete diventa fondamentale configurare un Domain Controller sul Server centrale.
Il DC è necessario per gestire l'autenticazione degli utenti e delle macchine all'interno del dominio e le autorizzazioni per accedere a specifiche risorse condivise. 

Ad esempio se un utente vuole accedere ad una stampante di rete appartenente al dominio, allora deve prima autenticarsi al Domain Controller tramite il protocollo di autenticazione scelto (tipo Kerberos), e solo successivamente la richiesta può essere inviata alla risorsa che si sta cercando di utilizzare.

I Domain Controller sono solitamente formati da questi componenti:
- __Directory Service Database__: per mantenere un archivio generale di tutti gli utenti e le risorse nel dominio.
- __Protocollo di autenticazione__: per consentire agli utenti di autenticarsi nel dominio.
- __Servizio per la sincronizzazione dei timestamp nella rete__: per sincronizzare gli orari su tutte le macchine in rete.
- __Server DNS__: per la risoluzione dei domain name degli host.

### Windows Server
Windows Server nasce con la possibilità di creare un dominio Active Directory in modo relativamente semplice, questo avviene tramite un software integrato che esegue tutta la configurazione di base in modo automatico tramite una serie di domande poste all'amministratore durante in processo. 
Windows Server, come tutti i prodotti Microsoft, necessita di una licenza a pagamento per poter essere utilizzato, per questo motivo l'utilizzo di ambienti Linux Server potrebbe essere un'ottima alternativa.

### Linux Server
Anche sui Server Linux è possibile installare e configurare un Domain Controller che svolge le stesse funzioni di un DC presente su Server Windows. Questo è possibile grazie al team di sviluppatori Samba che hanno creato degli specifici tools che ci consentono di configurare il server DNS, il protocollo di autenticazione e il Directory Service Database in modo semplice e analogo al processo di configurazione in ambiente Microsoft. 