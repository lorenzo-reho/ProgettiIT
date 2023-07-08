### Introduzione

Recentemente è stata individuata una nuova vulnerabilità all'interno del servizio Parse Server. La criticità della vulnerabilità è altissima considerando che l'exploit potrebbe consentire l'esecuzione di codice da remoto (Remote Code Execution) sulla macchina server che ospita il servizio.
La RCE può essere raggiunta tramite una __Prototype Pollution__ attuata sul __MongoDB BSON parser__ come spiegato nel [security advisory](https://github.com/advisories/GHSA-prm5-8g2m-24gg) di github.

### Parse Server

### Prototype Pollution:
