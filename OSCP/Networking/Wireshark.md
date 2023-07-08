
### Cosa ci consente di fare?
1) Debug servizi di rete
2) Catturare il traffico
3) Scoprire come funzionano tecnicamente i protocolli di rete

#### LIBRERIE UTILIZZATE:
* __Libpcap__ (Per ambienti Linux)
* Winpcap (Per ambienti Windows)

I dati raccolti durante la cattura del traffico possono essere filtrati e selezionati in un modo particolare tramite i _capture filters_ e _display filters_

Una macchina server solitamente possiede una o più interfacce di rete che generano e ricevono traffico.

Quando Wireshark viene avviato, ci chiede di selezionare su quale interfaccia di rete vogliamo eseguire la cattura dei pacchetti.

Wireshark ci consente di seguire gli stream di una determinata sessione TCP mettendo insieme i vari frammenti e visualizzando i dati che sono transitati da sorgente a destinazione.


#### CAPTURE FILTERS:

- Visualizzare solo i pacchetti riguardanti questo range di indirizzi:
```
net 10.11.1.0/24
```


#### DISPLAY FILTERS:

- Per visualizzare solo i pacchetti che si riferiscono al protocllo FTP
```
tcp.port == 21
```


