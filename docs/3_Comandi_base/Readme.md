# Introduzione a Openstack

---
**ATTENZIONE**

Per una soluzione grafica fate riferimento al corso CL010 che potete trovare qui: https://www.cl010.it

---
#### Convenzioni

Le convenzioni utilizzate saranno i nomi in minuscolo per gli utenti, quelli in maiuscolo indicheranno i progetti, le VM create termineranno in _VM.

## Parte 0: Dati amministratore
I dati di accesso alla nostra istanza Openstack potranno essere trovati nel file keystonerc_admin, che si trova in maniera conveniente nella home path del nostro utente sulla macchina in cui ci troviamo.

Per loggarci come utente admin su Openstack andiamo a utilizzare il comando **source** per caricare le variabili contenute nel file keystonerc:
```console
[user@machine] $ source ~/keystonerc_admin
[user@machine - (admin admin)] $ 
```
Come si può osservare qui sopra una delle env variables che vengono caricate modificano anche la variabile PS1 che, di conseguenza, modifica l'estetica della nostra shell per mostrarci quale utente abbiamo caricato in memoria e su quale progetto stiamo lavorando al momento attuale.

Facendo il **cat** del file keystonerc ci vengono mostrati i dati di configurazione dell'utente admin, da cui potremo ottenere la sua password nel caso volessimo accedere all'interfaccia web di openstack, reperibile localmente alla pagina http://localhost .

## Parte 1: Creazione utente e progetto da assegnargli
### Esercizio
**Creiamo un utente chiamato cl010u (User), cl010a (administrator del progetto) e assegnamoli ad un progetto CL010p**

### Soluzione
Possiamo listare tutti gli utenti dalla CLI con il comando
```console
[user@machine - (admin admin)] $ openstack user list
+----------------------------------+----------+
| ID                               | Name     |
+----------------------------------+----------+
| 2654ab37f5c89144d4ad05139266d51f | admin    |
| 32ec34aae8ea432e8af560a1cec02134 | glance   |
| 7047fcb790878asx76t7614bd72c972c | nova     |
+----------------------------------+----------+
```
Dove possiamo vedere che abbiamo già alcuni utenti che non abbiamo creato noi; questi utenti sono user di sistema, che vengono creati per ognuna delle parti che compongono openstack.

Creiamo quindi ora un nuovo progetto chiamato cl010p e due nuovi utenti, chiamati cl010u e cl010a, ai quali assegneremo il ruolo di \_\_member\_\_  e di admin rispettivamente:
```console
[user@machine - (admin admin)] $ openstack project create cl010p --domain default
[user@machine - (admin admin)] $ openstack user create --project cl010p --password pass cl010a
[user@machine - (admin admin)] $ openstack user create --project cl010p --password word cl010u
[user@machine - (admin admin)] $ openstack project list
+----------------------------------+--------------------+
| ID                               | Name               |
+----------------------------------+--------------------+
| 0b0b995694234521bf93c792ed44247f | cl010p             |
| 29c09e68e6f741afa952a837e29c700b | admin              |
+----------------------------------+--------------------+
[user@machine - (admin admin)] $ openstack role add --user cl010a --project ID_PROGETTO admin
[user@machine - (admin admin)] $ openstack role add --user cl010u --project ID_PROGETTO __member__
```

All'utente **CL010u** abbiamo assegnato il ruolo di \_\_member\_\_ che è il ruolo con i privilegi più bassi in assoluto ma che ha comunque un buon margine di manovra per quanto riguarda quanto può fare.

Una volta fatto questo ripetiamo il processo anche per un secondo utente, al quale però assegneremo il ruolo di admin del progetto, in modo da non dover utilizzare sempre l'amministratore di Openstack e iniziare quindi a capire le limitazioni assegnate ai vari utenti.


## Parte 2: Caricamento di un sistema operativo
### Esercizio
**Caricare un'immagine di un sistema operativo**
### Soluzione
Prima di tutto dobbiamo caricare un sistema operativo funzionante all'interno del nostro sistema e qui andremo a caricare un'immagine molto particolare chiamata CirrOS, un sistema operativo Linux completo ma minimale, utile per testare il funzionamento di un sistema operativo in cloud; può essere reperito [qui](http://download.cirros-cloud.net/):

```console
[user@machine - (cl010u cl010)] $ openstack image create --file cirros.qcow2 --format qcow2 cirros
```

Ora possiamo lanciare la nostra istanza:

## Parte 3: Creazione di un'istanza da riga di comando
### Esercizio
- **Accedere via command line a Openstack, utilizzando i file keystonerc** <br>
- **Caricare un ISO che nono sia cirros su Openstack** <br>
- **Creare un'istanza da riga di comando**

### Soluzione
#### Accedere via command line
Come visto sopra i files keystonerc caricano delle env variables che ci permettono di accedere facilmente alla nostra istanza di Openstack da riga di comando senza bisogno di ricordarci password o altro; per caricarle in memoria basta dare:
```console
[user@machine] $ source keystonerc_cl010u
[user@machine - (cl010u cl010)] $ 
```

Ora vediamo le macchine virtuali create con il comando qui sotto mostrato, che ci ritornerà una lista delle VM create in precedenza,ovviamente delle quali ho visibilità come utente cl010u (siccome sono entrato con questo utente caricando il suo keystonerc).
```console
[user@machine - (cl010u cl010)] $ openstack server list
```
Come potrete vedere, vi verrà ritornata la macchina creata nell'esercizio precedente, cl010_VM1.
#### caricare un ISO su Openstack
Per questo esercizio ho precedentemente scaricato un'immagine della distribuzione fedora creata appositamente per il cloud, che ora andremo a caricare su Openstack come visto in precedenza:
```console
[user@machine - (cl010u cl010)] $ openstack image create --file fedoracloud.qcow2 --format qcow2 fedoracloud
```
Una volta dato il comando ci verrà fornita in output la tabella con i valori che identificano le impostazioni dell'immagine caricata.

#### Creare un'istanza da riga di comando
Possiamo dare un'occhiata a tutti i comandi che possiamo usare su Openstack tramite il comando help, come mostrato nel blocco qui sotto; questo comando verrà dato in pasto ad un paginatore (less) che ci permette di visualizzare con facilità le informazioni ritornateci: 
```console
[user@machine - (cl010u cl010)] $ openstack help server create | less

```
Inoltre necessitiamo di avere una rete pronta, che potremo creare così:

```console
[user@machine - (cl010u cl010)] $ openstack network create intNetcl010
```


Una volta osservato tutte le opzioni decidiamo di creare una macchina come quella precedente
```console
[user@machine - (cl010u cl010)] $ openstack server create --image fedoracloud --flavour m1.tiny --network intNetcl010 cl010_VM1
```
Questo comando creerà una macchina chiamata cl010_VM1 sul network creato precedentemente (vedasi parte 2 di questo documento), con l'immagine di fedora cloud e con m1.tiny come flavour.

Fatto questo ci verrà ritornata una tabella con i parametri della VM appena creata e,attendendo qualche secondo, diamo nuovamente il comando **openstack server list** per verificare che la creazione sia andata a buon fine: se tutto è ok la VM1 dovrebbe avere lo status ACTIVE.

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
