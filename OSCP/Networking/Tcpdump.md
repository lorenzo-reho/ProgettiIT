Tool da riga di comando

### Cosa consente di fare?
1) Catturare il traffico
2) Analizzare dei file pcap già esistenti

```
tcpdump -r file.pcap
```

L'output di tcpdump può essere veramente complicato da leggere, quindi spesso viene passato ad altri programmi per filtrare le informazioni da reperire.

- Per evitare la risoluzione dei domini (-n)
```
tcpdump -n -r file.pcap
```

- Per filtrare sulla base della sorgente (src host)
```
tcpdump -n src host 192.168.100.5 -r file.pcap
```

- Per filtrare sulla base della destinazione (dst host)
```
tcpdump -n dst host 192.168.100.5 -r file.pcap
```

- Per filtrare sulla base della porta/protocollo (port)
```
tcpdump -n port 21 -r file.pcap
```


tcpdump ci consente anche di applicare dei filtri avanzati sull header delle richieste TCP.

- Per filtrare solo le richieste con i bit ACK and PSH settati
```
tcpdump -A -n 'tcp[13]=24' -r file.pcap
```

