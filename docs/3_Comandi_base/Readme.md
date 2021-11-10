# Introduzione a Openstack

---
**ATTENZIONE**

Per una soluzione grafica fate riferimento al corso CL010 che potete trovare qui: https://www.cl010.it

---
## Parte 0: Dati amministratore
I dati di accesso alla nostra istanza Openstack potranno essere trovati nel file keystonerc_admin, che si trova in maniera conveniente nella /root/ del nostro sistema operativo.

Accediamo ora come utente sudo e andiamo a vedere il dato che ci interessa:
```console
[user@machine] $ su
[root@machine] # cat /root/keystonerc_admin
```
Questo ci mostra i dati di configurazione dell'utente admin, da cui potremo ottenere la sua password e loggarci alla pagina http://localhost dove ci verrà esposto Openstack; qui accederemo quindi con il nome utente admin e la password trovata al passo precedente; più avanti nella parte 3 di questo documento verrà mostrato come utilizzare direttamente questo file per accedere via riga di comando ad Openstack.

Le convenzioni utilizzate saranno i nomi in minuscolo per gli utenti, quelli in maiuscolo indicheranno i progetti, le VM create termineranno in _VM.

## Parte 1: Creazione utente e progetto da assegnargli
### Esercizio
**Creiamo un utente chiamato cl010u (User), cl010a (administrator del progetto) e assegnamoli ad un progetto CL010p**

### Soluzione
Dalla dashboard che ci ritroviamo quando entriamo come utente admin possiamo creare un nuovo progetto accedendo alle tab (in ordine) 

> identity &#8594; project &#8594; Create project

Qui possiamo creare un progetto chiamato CL010p.

Una volta creato il progetto cambiamo tab e sempre sotto identity troveremo users dove possiamo vedere che è già stato in parte popolato da vari utenti creati dai servizi base di Openstack come cinder, nova e altri che vedremo più avanti.

A destra troveremo il pulsante Create user che ci aprirà un menù a tendina a cui forniremo tutte le indicazioni del caso tra cui anche a quale/i progetto/i assegnare il nostro utente e quale sarà il suo ruolo: ricordiamo che Openstack è amministrato tramite role based access control, ossia i ruoli definiscono cosa possono e non possono richiedere, vedere o fare gli utenti.

Al nostro nuovo utente **CL010u** assegneremo il ruolo di \_\_member\_\_ che è il ruolo con i privilegi più bassi in assoluto ma che ha comunque un buon margine di manovra per quanto riguarda le cose che possiamo fare.

Una volta fatto questo ripetiamo il processo anche per un secondo utente, al quale però assegneremo il ruolo di admin del progetto, in modo da non dover utilizzare sempre l'amministratore di Openstack e iniziare quindi a capire le limitazioni assegnate ai vari utenti.

Slogghiamoci ora dall'utente admin che abbiamo usato fin'ora e prepariamoci al prossimo passo.

## Parte 2: Creazione di istanze
### Esercizio
**Creare un'istanza su Openstack con un sistema operativo funzionante**
### Soluzione
Prima di tutto dobbiamo caricare un sistema operativo funzionante all'interno del nostro sistema, pertanto accediamo con l'utente amministratore **cl010a** andiamo su 
> project &#8594; compute &#8594; images

E qui andremo a caricare un'immagine molto particolare chiamata CirrOS, un sistema operativo Linux completo ma minimale, utile per testare il funzionamento di un sistema operativo in cloud; può essere reperito [qui](http://download.cirros-cloud.net/); nel caso voleste farlo da riga di comando nell'esercizio successivo (seconda sezione) viene indicato come fare.

Inoltre necessitiamo di avere una rete pronta, pertanto facciamo riferimento alla sezione [4 sul networking](../4_Networking/Readme.md) per fornirvi tutte le nozioni necessarie per creare correttamente la rete; materiale aggiuntivo può essere trovato [a questo link](https://creodias.eu/-/how-to-create-a-network-with-router-in-horizon-dashboard-).

Accediamo ora con il nostro utente **cl010u** precedentemente creato e una volta dentro ci dirigiamo nella sezione

> project &#8594; compute &#8594; instances

Un'importante distinzione, non __creiamo__ le istanze ma bensì le __lanciamo__ in quanto non sono come le vecchie macchine virtuali che richiedevano spazio dedicato e fisso, qui è tutto più rapido e fatto seguendo la logica cloud.

Detto questo lanceremo la nostra istanza con il pulsante **launch instance** che ci aprirà un menù a tendina con tutti i vari elementi che ci servono per lanciare la nostra macchina; assegneremo alla macchina il nome di CL010_VM1 e il nostro sistema ci mostra quante VM abbiamo creato fin'ora e quante possiamo crearne seguendo le limitazioni imposte al progetto dall'amministratore (il nostro utente admin usato in precedenza).

Fatto questo ci spostiamo nella tab  **source** sottostante dove possiamo selezionare l'immagine da utilizzare (per questo test consigliamo di mettere NO al selettore di  **create new volume** e selezioneremo come immagine CirrOS cliccando la freccetta corrispondente; questo sposterà CirrOS in una nuova riga con intestazione Allocated che ci segnala che verrà allocata una macchina virtuale con questo sistema operativo deployato sopra.

Passiamo ora nella tab subito sotto chiamata **flavour** che ci indica quale tipologia di virtual hardware vogliamo che la nostra virtual machine abbia, solitamente vengono pre-creati dall'installazione; nel nostro caso è consigliato l'uso di m1.tiny in quanto l'OS è estremamente piccolo e richiede pochissime risorse.

Infine la terza cosa fondamentale per la corretta creazione di una VM ossia la rete: selezioniamo quindi una rete creata come indicato in precedenza e possiamo selezionare **launch instance**.

Ovviamente la nostra istanza non partirà istantaneamente in quanto richede qualche secondo o minuto, dipendentemente dalle richieste hardware, per essere creata; una volta terminato questo processo nella dashboard di fronte a noi vedremo Ready nella colonna status, indicandoci che è tutto pronto per essere lanciato.

## Parte 3: Caricamento di impostazioni e immagini da riga di comando
### Esercizio
- **Accedere via command line a Openstack, utilizzando i file keystonerc** <br>
- **Caricare un ISO su Openstack** <br>
- **Creare un'istanza da riga di comando**

### Soluzione
#### Accedere via command line
I files keystonerc caricano delle env variables che ci permettono di accedere facilmente alla nostra istanza di Openstack da riga di comando senza bisogno di ricordarci password o altro; per caricarle in memoria basta dare
```console
[user@machine] $ source keystonerc_cl010u
[user@machine - (cl010u cl010)] $ 
```
Come si può osservare qui sopra una delle env variables che vengono caricate modificano anche la variabile PS1 che, di conseguenza, modifica l'estetica della nostra shell per mostrarci quale utente abbiamo caricato in memoria e su quale progetto stiamo lavorando al momento attuale.

Ora vediamo le macchine virtuali create con il comando qui sotto mostrato, che ci ritornerà una lista delle VM create in precedenza,ovviamente delle quali ho visibilità come utente cl010u (siccome sono entrato con questo utente caricando il suo keystonerc).
```console
[user@machine - (cl010u cl010)] $ openstack server list
```
Come potrete vedere, vi verrà ritornata la macchina creata nell'esercizio precedente, cl010_VM1.
#### caricare un ISO su Openstack
Per caricare un iso su Openstack useremo il comando, nel nostro caso ho precedentemente scaricato un'immagine della distribuzione fedora creata appositamente per il cloud
```console
[user@machine - (cl010u cl010)] $ openstack image create --file fedoracloud.qcow2 --format qcow2 fedoracloud
```
Una volta dato il comando ci verrà fornita in output una tabella con una serie di valori che identificano le impostazioni dell'immagine caricata.

#### Creare un'istanza da riga di comando
Possiamo dare un'occhiata a tutti i comandi che possiamo usare su Openstack tramite il comando help, come mostrato nel blocco qui sotto; questo comando verrà dato in pasto ad un paginatore (less) che ci permette di visualizzare con facilità le informazioni ritornateci: 
```console
[user@machine - (cl010u cl010)] $ openstack help server create | less
```
Una volta osservato tutte le opzioni decidiamo di creare una macchina come quella precedente
```console
[user@machine - (cl010u cl010)] $ openstack server create --image cirros --flavour m1.tiny --network intNetcl010 cl010_VM2
```
Questo comando creerà una macchina chiamata cl010_VM2 sul network creato precedentemente (vedasi parte 2 di questo documento), con la stessa immagine battezzata cirros e con m1.tiny come flavour.

Fatto questo ci verrà ritornata una tabella con i parametri della VM appena creata e,attendendo qualche secondo, diamo nuovamente il comando **openstack server list** per verificare che la creazione sia andata a buon fine: se tutto è ok la VM2 dovrebbe avere lo status ACTIVE.

Per verificare il corretto funzionamento delle macchine basterà andare sul pannello di controllo nella sezione delle istanze e aprire la tendina della colonna action della VM che ci interessa; qui tra i vari comandi troveremo console che ci attaccherà direttamente alla console della nostra macchina dove in questo caso ci verrano forniti a login i dati d'accesso in quanto cirrOS ha questa feature per facilitare il testing delle VM; questa feature è estremamente insicura per un ambiente di produzione e consigliamo l'uso del sistema cirrOS esclusivamente in ambito di testing.

## Parte 4: Generazione di chiavi SSH
### Esercizio
**Creiamo o carichiamo delle chiavi SSH nella nostra istanza di Openstack**
### Soluzione
Andando nella tab

> project &#8594; compute &#8594; key pairs

Si possono caricare (ovviamente la parte pubblica) o creare (verrà fatta scaricare la parte privata della chiave mentre la pubblica verrà direttamente caricata) chiavi SSH per poter accedere all'istanza in modo passwordless. 
Da command line invece posso fare la stessa operazione con il comando (NB: è un comando unico, l'escape serve solo ad indicare che stiamo andando a capo, stessa cosa per il maggiore sulla seconda riga, non devono essere riportati e dovete dare il comando senza andare a capo).
```console
[user@machine - (cl010u cl010)] $ openstack keypair create --private-key <nome da dare alla private key> \
> --public-key <nome da dare alla chiave pubblica>
```
Questo comando salverà nella path in cui siamo la chiave pubblica e quella privata, oltre a caricare direttamente la pubblica in Openstack.
