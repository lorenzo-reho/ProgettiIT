
### 1. Creazione e configurazione Macchina Windows Server 2019

- Set hostname della macchina
```shell
Rename-Computer -NewName DC1 -Restart
```

- Visualizza informazioni riguardo le interfacce di rete
```shell
Get-NetAdapter
```

- Set IP Statico
```bash
New-NetIPAddress -InterfaceAlias Ethernet0 -IPAddress 192.168.44.253 -AddressFamily IPv4 -PrefixLength 24 –DefaultGateway 192.168.44.254
```

- Set DNS Server
```bash
Set-DnsClientServerAddress -InterfaceAlias Ethernet0 -ServerAddresses 192.168.44.253
```

- Installazione AD-Domain-Services
```bash
Install-WindowsFeature AD-Domain-Services -IncludeManagementTools
```

- Importazione modulo **ADDSDeployment**
```bash
Get-Command -Module ADDSDeployment
```

- Creazione foresta e dominio
```bash
Install-ADDSForest
```


