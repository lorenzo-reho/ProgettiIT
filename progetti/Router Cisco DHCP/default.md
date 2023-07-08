
### Scenario di rete:


![schema](imgs/schema.JPG)

Informazioni rete:
- __Indirizzo di rete__: 192.168.10.0
- __Subnet Mask__: 255.255.255.0
- __Gateway__: 192.168.10.1

L'obiettivo è quello di configurare il Router R1 come server DHCP in grado di assegnare indirizzo IP, Gateway e Subnet Mask a tutti i dispositivi della rete che richiedono configurazione dinamica mediante DHCP.
Notiamo anche la presenza di due server (Web Server e File Server) che DEVONO essere configurati staticamente, per questo motivo bisogna escludere dal set di indirizzi IP assegnabili dal server DHCP tutti quelli configurati a mano dall'amministratore, in questo caso sono:
- Indirizzo IP del Gateway (192.168.10.1)
- Indirizzo IP del Web Server (192.168.10.2)
- Indirizzo IP del File Server (192.168.10.3)

### Configurazione DHCP su Router Cisco

1) Entrare in modalità privilegiata.

```cisco
Router> enable
```

2) Entrare in modalità configurazione.

```cisco
Router# config terminal
```

3) Escludere il range (192.168.10.1-192.168.10.3) dagli indirizzi IP assegnabili dal DHCP.

```cisco
Router(config)# ip dhcp excluded-address 192.168.10.1 192.168.10.3
```

4) Creare un pool DHCP e nominarlo arbitrariamente.

```cisco
Router(config)# ip dhcp pool hackpills
```

5) Definire la rete all'interno della quale dispensare gli indirizzi. 

```cisco
Router(dhcp-config)# network 192.168.10.0 255.255.255.0
```

6) Definire quale gateway assegnare ai dispositivi della rete.

```cisco
Router(dhcp-config)# default-router 192.168.10.1
```

### Comandi bonus

- Per definire un Server DNS.

```cisco
Router(dhcp-config)# dns-server <ip-server-dns>
```

- Per definire un Domain Name.

```cisco
Router(dhcp-config)# domain-name <nome del dominio>
```
