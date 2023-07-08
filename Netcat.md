# Netcat

## Cos'è Netcat?
TODO

## Comandi fondamentali:

Per aprire una porta sulla macchina locale e mettere sullo stream il contenuto di un file:

```bash
nc -lnvp port < file
```

Per connettersi ad una porta e ricevere il contenuto di uno stream:

```bash
nc ip port > file
```
