---
title: Cosa sono le VLAN? Tipi, vantaggi e routing Inter-VLAN
published: true
visible: true
highlight:
    theme: obsidian
metadata:
    keywords: 'VLAN, Cisco, Cisco VLAN, Tipi di VLAN, Routing Inter-VLAN, CCNA, Dominio di Broadcast, Sicurezza, porte trunk, porte access, legacy, on a stick, switch layer 3'
---

### Cos'è una VLAN?

Le VLAN  sono uno strumento che consentono all'amministratore di rete di suddividere una rete locale in più segmenti indipendenti tra loro. In questo modo è possibile gestire la rete in maniera più efficiente perchè si possono raggruppare i dispositivi tra cui esiste una correlazione logica (amministratori, docenti, studenti ecc...) e separare il __dominio di broadcast__ di ogni segmento.

**_NOTA:_**  Il _dominio di broadcast_ è l'insieme dei dispositivi contenuti all'interno di una rete, sottorete o VLAN, tale per cui se uno di questi dispositivi dovesse inviare un frame in broadcast, sarebbe ricevuto da tutti gli altri.

Ogni VLAN possiede un proprio indirizzo di rete e per far comunicare VLAN differenti è necessario utilizzare un Router o Switch Layer 3.

Le VLAN si appoggiano allo standard 802.1Q che definisce tutte le regole e i protocolli che consentono la creazione, gestione e funzionamento delle VLAN.

#### ESEMPIO DI SCENARIO DI RETE CON 2 VLAN

![general-vlan](imgs/general-vlan.PNG)

Un eventuale messaggio in broadcast inviato da un dispositivo appartenente alla VLAN 10 sarebbe ricevuto da tutti gli altri dispositivi che fanno parte della stessa VLAN, anche se si trovano su switch differenti.

### Quali sono i vantaggi delle VLAN?

- __RIDUZIONE DEL DOMINIO DI BROADCAST__: I frame mandati in broadcast da dispositivi che fanno parte di una VLAN possono essere raggiunti solo da altri dispositivi appartenenti alla stessa VLAN.
- __SICUREZZA__: Si possono creare delle VLAN per gruppi di utenti particolari in modo tale da isolarne il traffico e quindi diminuire la probabilità che qualche malintenzionato lo intercetti.
- __COSTO__: Non è necessario acquistare ulteriori dispositivi di rete come switch o router per creare delle altre reti isolate.
- __VELOCITÀ__: Un dominio di broadcast più piccolo e una minore quantità di traffico comporta un aumento di velocità all'interno della rete.

#### Cosa sono le porte Trunk?
Le porte trunk collegano i dispositivi intermedi (Switch, Router) e consentono di trasmettere il traffico generato da diverse VLAN, in questo modo è possibile mettere in comunicazione disposivi appartenenti alla stessa VLAN ma collegati a _switch_ differenti.
Considerando che da questi _canali_ viaggiano frame corrispondenti a VLAN diverse è necessario tenere traccia, per ognuno di essi, quale VLAN l'ha generati, per questo motivo viene aggiunto un campo __TAG__ di 4 byte che contiene l'ID della VLAN in cui è contenuto il dispositivo che ha generato il frame.

- In questa immagine le porte __A__, __B__ e __C__ sono di tipo __TRUNK__

![trunk](imgs/trunk.PNG)

#### Cosa sono le porte access?
Le porte access sono delle porte che collegano gli switch ai dispositivi finali (PC, Laptop ecc...), vengono definite anche _untagged ports_ perchè quando il frame attraversa questo genere di porte è di tipo _untagged_ (senza campo TAG). Nel momento in cui un frame arriva allo switch e deve raggiungere un dispositivo di destinazione direttamente collegato, allora viene eliminato il campo _TAG_ e inviato il frame attraverso la porta access. Analogamente quando una macchina genera un frame che deve raggiungere un altro dispositivo nella stessa VLAN (magari anche su un switch differente) questo viene inviato allo switch tramite la porta _access_, che aggiunge il campo _TAG_ e spedisce il frame. 

- In questa immagine le porte __A__  e __B__ sono di tipo __ACCESS__

![access](imgs/access.PNG)

### Tipologie di VLAN:

- __VLAN Predefinita__: Quando viene acquistato uno switch nuovo tutte le sue porte sono associate a questa particolare VLAN. Il numero identificativo della VLAN Predefinita è 1 e non può essere modificata rinominata o eliminata.
- __VLAN Gestione__: VLAN utilizzata dall'amministratore per generare traffico di gestione dei dispositivi di rete tramite protocolli tipo Telnet, SSH, SNMP ecc...
- __VLAN Dati__: VLAN utilizzata per raggruppare gli utenti e isolare il loro traffico di rete. In questo modo gli utenti che devono navigare su internet, richiedere pagine web, inviare e ricevere email, possono farlo in un ambiente più ristretto e controllato senza far venire meno la sicurezza di altri gruppi con un ruolo più sensibile.
- __VLAN Nativa__: Gli switch legacy non hanno la possibilità di aggiungere il campo tag nella trama ethernet, perciò tutti i frame untagged vengono immediatamente classificati come frame generati da una VLAN nativa che di default corrisponde a quella predefinita. 
- __VLAN Voce__: Utile per separare il traffico telefonico da quello generale in modo tale da attribuirgli una maggiore priorità e renderlo di conseguenza più veloce ed affidabile. 

### Tipologie di Routing inter-vlan:
Come abbiamo accennato precedentemente, i dispositivi appartenenti a VLAN differenti non possono comunicare tra di loro perchè corrispondono a diversi indirizzi di rete, esistorno però delle tecniche di _routing inter-VLAN_ utili a superare questo vincolo.

#### LEGACY
Si utilizza un router e si configura una diversa interfaccia per ogni vlan che deve partecipare alla comunicazione, ovviamente questo comporta la necessità di utilizzare numerose interfacce sul router e cavi Ethernet. L'indirizzo IP di ogni interfaccia corrisponde al gateway per i dispositivi presenti nella VLAN associata.

![leagacy](imgs/legacy.PNG)

#### ON A STICK
Si utilizza un router e si suddivide una singola interfaccia in diverse sottointerfacce, una per ogni vlan necessaria. In questo modo si limita notevolmente il numero di interfacce fisiche e cavi da utilizzare. L'indirizzo IP di ogni sottointerfaccia corrisponde al gateway dei dispositivi contenuti all'interno della VLAN associata.


![on-a-stick](imgs/on-a-stick.PNG)

__A.10__ ed __A.20__ sono sottointerfacce di __A__

#### SWITCH LAYER-3
Si utilizza uno switch Layer 3 (che può funzionare sia come dispositivo di livello 2 che come dispositivo di livello 3) e si crea una SVI (Switch Virtual Interface) per ogni vlan, l'indirizzo IP di ogni interfaccia virtuale dello switch corrisponde al gateway dei dispositivi contenuti all'interno della VLAN corrispondente.

![layer-3](imgs/layer-3.PNG)
