# Introduzione a Openstack
## Parte 0: Dati amministratore
I dati di accesso alla nostra istanza openstack potranno essere trovati nel file keystonerc_admin, che si trova in maniera conveniente nella /root/ del nostro sistema operativo.

Accediamo ora come utente sudo e andiamo a vedere il dato che ci interessa:
```console
[user@machine] $ su
[user@machine] $ cat /root/keystonerc_admin
```
Questo ci mostra i dati di configurazione dell'utente admin, da cui potremo ottenere la sua password e loggarci alla pagina http://localhost dove ci verrà esposto Openstack; qui accederemo quindi con il nome utente admin e la password trovata al passo precedente; più avanti nella parte 3 di questo documento verrà mostrato come utilizzare direttamente questo file per accedere via riga di comando ad Openstack.

Le convenzioni utilizzate saranno i nomi in minuscolo per gli utenti, quelli in maiuscolo indicheranno i progetti, le VM create termineranno in _VM.

## Parte 1: Creazione utente e progetto da assegnargli
### Esercizio
**Creiamo un utente chiamato cl010u (User), cl010a (administrator del progetto) e assegnamoli ad un progetto CL010p**

### Soluzione
Dalla dashboard che ci ritroviamo quando entriamo come utente admin possiamo creare un nuovo progetto accedendo alle tab (in ordine) 
```
identity -> project -> Create project
```
Qui possiamo creare un progetto chiamato CL010p.

Una volta creato il progetto cambiamo tab e sempre sotto identity troveremo users dove possiamo vedere che è già stato in parte popolato da vari utenti creati dai servizi base di openstack come cinder, nova e altri che vedremo più avanti.

A destra troveremo il pulsante Create user che ci aprirà un menù a tendina a cui forniremo tutte le indicazioni del caso tra cui anche a quale/i progetto/i assegnare il nostro utente e quale sarà il suo ruolo: ricordiamo che Openstack è amministrato tramite role based access control, ossia i ruoli definiscono cosa possono e non possono richiedere, vedere o fare gli utenti.

Al nostro nuovo utente **CL010u** assegneremo il ruolo di \_\_member\_\_ che è il ruolo con i privilegi più bassi in assoluto ma che ha comunque un buon margine di manovra per quanto riguarda le cose che possiamo fare.

Una volta fatto questo ripetiamo il processo anche per un secondo utente, al quale però assegneremo il ruolo di admin del progetto, in modo da non dover utilizzare sempre l'amministratore di openstack e iniziare quindi a capire le limitazioni assegnate ai vari utenti.

Slogghiamoci ora dall'utente admin che abbiamo usato fin'ora e prepariamoci al prossimo passo.

## Parte 2: Creazione di istanze
### Esercizio
**Creare un'istanza su Openstack con un sistema operativo funzionante**
### Soluzione
Prima di tutto dobbiamo caricare un sistema operativo funzionante all'interno del nostro sistema, pertanto accediamo con l'utente amministratore **cl010a** andiamo su 
```
project -> compute -> images
```
E qui andremo a caricare un'immagine molto particolare chiamata CirrOS, un sistema operativo Linux completo ma minimale, utile per testare il funzionamento di un sistema operativo in cloud; può essere reperito [qui](http://download.cirros-cloud.net/); nel caso voleste farlo da riga di comando nell'esercizio successivo (seconda sezione) viene indicato come fare.

Inoltre necessitiamo di avere una rete pronta, pertanto facciamo riferimento alla sezione [4 sul networking](../4_Networking/Readme.md) per fornirvi tutte le nozioni necessarie per creare correttamente la rete; materiale aggiuntivo può essere trovato [a questo link](https://creodias.eu/-/how-to-create-a-network-with-router-in-horizon-dashboard-).

Accediamo ora con il nostro utente **cl010u** precedentemente creato e una volta dentro ci dirigiamo nella sezione
```
project -> compute -> instances
```
Un'importante distinzione, non __creiamo__ le istanze ma bensì le __lanciamo__ in quanto non sono come le vecchie macchine virtuali che richiedevano spazio dedicato e fisso, qui è tutto più rapido e fatto seguendo la logica cloud.

Detto questo lanceremo la nostra istanza con il pulsante **launch instance** che ci aprirà un menù a tendina con tutti i vari elementi che ci servono per lanciare la nostra macchina; assegneremo alla macchina il nome di CL010_VM1 e il nostro sistema ci mostra quante VM abbiamo creato fin'ora e quante possiamo crearne seguendo le limitazioni imposte al progetto dall'amministratore (il nostro utente admin usato in precedenza).

Fatto questo ci spostiamo nella tab  **source** sottostante dove possiamo selezionare l'immagine da utilizzare (per questo test consigliamo di mettere NO al selettore di  **create new volume** e selezioneremo come immagine CirrOS cliccando la freccetta corrispondente; questo sposterà CirrOS in una nuova riga con intestazione Allocated che ci segnala che verrà allocata una macchina virtuale con questo sistema operativo deployato sopra.

Passiamo ora nella tab subito sotto chiamata **flavour** che ci indica quale tipologia di virtual hardware vogliamo che la nostra virtual machine abbia, solitamente vengono pre-creati dall'installazione; nel nostro caso è consigliato l'uso di M1.tiny in quanto l'OS è estremamente piccolo e richiede pochissime risorse.

Infine la terza cosa fondamentale per la corretta creazione di una VM ossia la rete: selezioniamo quindi una rete creata come indicato in precedenza e possiamo selezionare **launch instance**.

Ovviamente la nostra istanza non partirà istantaneamente in quanto richede qualche secondo o minuto, dipendentemente dalle richieste hardware, per essere creata; una volta terminato questo processo nella dashboard di fronte a noi vedremo Ready nella colonna status, indicandoci che è tutto pronto per essere lanciato.

## Parte 3: Caricamento di impostazioni e immagini da riga di comando
### Esercizio
**- Accedere via command line a Openstack, utilizzando i file keystonerc <br>
- caricare un ISO su Openstack <br>
- Creare un'istanza da riga di comando
**
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
[user@machine - (cl010u cl010)] $ openstack image create --file cirros
```

#### Creare un'istanza da riga di comando
Possiamo dare un'occhiata a tutti i comandi che possiamo usare su Openstack tramite il comando help, come mostrato nel blocco qui sotto; questo comando verrà dato in pasto ad un paginatore (less) che ci permette di visualizzare con facilità le informazioni ritornateci: 
```console
[user@machine - (cl010u cl010)] $ openstack help server create | less
```
Una volta osservato tutte le opzioni decidiamo di creare una macchina come quella precedente

