# MYSQL
### Cos'è MySql
TODO

## Comandi Fondamentali

Installazione MySql:

```bash
sudo apt update
sudo apt upgrade
sudo apt install mysql-server
```

start/stop/restart/status del DBMS:

```bash
sudo /etc/init.d/mysql start
sudo /etc/init.d/mysql stop
sudo /etc/init.d/mysql restart
sudo /etc/init.d/mysql status
```

Per accedere al dbms:

```bash
mysql -u <username> -p
```

Creazione di un utente:

```mysql
mysql> CREATE USER '<username>'@'<host>' IDENTIFIED BY '<password>';
```

Creazione di un database:

```mysql
mysql> CREATE DATABASE IF NOT EXISTS <dbname>;
```

Per dare i privilegi ad un utente a tutte le tabelle di un db:

```mysql
mysql> GRANT ALL PRIVILEGES ON <dbname>.* TO '<username>'@'<host>';
mysql> FLUSH PRIVILEGES;
```

Per mostrare tutti gli utenti disponibili:

```mysql
mysql> SELECT ... FROM mysql.user;
```


## LINK UTILI:
[MySQL](https://dev.mysql.com/doc/refman/8.0/en/).