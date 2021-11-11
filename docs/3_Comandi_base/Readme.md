# Introduzione a Openstack

> **ATTENZIONE** <br>
> Per una soluzione grafica fate riferimento al corso CL010 che potete trovare qui: https://www.cl010.it <br>
> Inoltre per favorire la leggibilità non tutti gli output a schermo verranno mostrati.

---

#### Convenzioni

Le convenzioni utilizzate saranno i nomi in minuscolo per gli utenti, quelli in maiuscolo indicheranno i progetti, le VM create termineranno in _VM.

## Parte 0: Dati amministratore
I dati di accesso alla nostra istanza Openstack potranno essere trovati nel file keystonerc_admin, i quali contengono delle env variables che identificano l'utente che vogliamo utilizzare e a quale progetto vogliamo connetterci; sono estremamente comodi perché ci permettono di swappare tra loro con estrema facilità e velocità <br>
I files keystonerc vengono generati in /root/ sulla macchina in cui ci troviamo, per maggiore comodità si consiglia di copiarle nella path del nostro end user e di assegnargliele per fare in modo che possano essere utilizzate:
```console
[user@machine] $ sudo cp /root/keystonerc_admin .
[user@machine] $ sudo chown user keystonerc_admin
```

Per loggarci come utente admin su Openstack andiamo a utilizzare il comando **source** per caricare le variabili contenute nel file keystonerc:
```console
[user@machine] $ source ~/keystonerc_admin
[user@machine - (admin admin)] $ 
```
Come si può osservare qui sopra una delle env variables che vengono caricate modificano anche la variabile PS1 che, di conseguenza, modifica l'estetica della nostra shell per mostrarci quale utente abbiamo caricato in memoria e su quale progetto stiamo lavorando al momento attuale.

Facendo il **cat** del file keystonerc ci vengono mostrati i dati di configurazione dell'utente admin, da cui potremo ottenere la sua password nel caso volessimo accedere all'interfaccia web di openstack, reperibile localmente alla pagina http://localhost .

## Parte 1: Creazione utente e progetto da assegnargli
### Esercizio
**Creiamo un utente chiamato cl010u (User), cl010a (administrator del progetto), assegnamoli ad un progetto CL010p e creiamo i corrispondenti files keystonerc**

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

Andiamo ora a creare i files keystonerc per entrambi gli user a partire da quelli che già abbiamo:

## Parte 2: Caricamento di un sistema operativo
### Esercizio
**Caricare un'immagine di un sistema operativo**
### Soluzione
Prima di tutto dobbiamo caricare un sistema operativo funzionante all'interno del nostro sistema e qui andremo a caricare un'immagine molto particolare chiamata CirrOS, un sistema operativo Linux completo ma minimale, utile per testare il funzionamento di un sistema operativo in cloud; può essere reperito [qui](http://download.cirros-cloud.net/):

```console
[user@machine - (cl010u cl010)] $ openstack image create --file cirros.qcow2 --disk-format qcow2 cirros
```

Ora possiamo lanciare la nostra istanza ma prima aggiungiamo le chiavi SSH:

## Parte 3: Generazione di chiavi SSH
### Esercizio
**Creiamo o carichiamo delle chiavi SSH nella nostra istanza di Openstack**
### Soluzione

Da command line invece posso fare la stessa operazione con il comando (NB: è un comando unico, l'escape serve solo ad indicare che stiamo andando a capo, stessa cosa per il maggiore sulla seconda riga, non devono essere riportati e dovete dare il comando senza andare a capo).
```console
[user@machine - (cl010u cl010)] $ openstack keypair create --private-key <nome da dare al file che contiene la private key> \
> --public-key <nome da dare alla chiave pubblica>
```
Questo comando salverà nella path in cui siamo la chiave pubblica e quella privata, oltre a caricare direttamente la pubblica in Openstack.


## Parte 4: Creazione di un'istanza da riga di comando
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
#### caricare un ISO su Openstack
Per questo esercizio ho precedentemente scaricato un'immagine della distribuzione fedora creata appositamente per il cloud, che ora andremo a caricare su Openstack come visto in precedenza:
```console
[user@machine - (cl010u cl010)] $ openstack image create --file fedoracloud.qcow2 --disk-format qcow2 fedoracloud
```
Una volta dato il comando ci verrà fornita in output la tabella con i valori che identificano le impostazioni dell'immagine caricata.

#### Creare un'istanza da riga di comando
Possiamo dare un'occhiata a tutti i comandi che possiamo usare su Openstack tramite il comando help, come mostrato nel blocco qui sotto; questo comando verrà dato in pasto ad un paginatore (less) che ci permette di visualizzare con facilità le informazioni ritornateci: 
```console
[user@machine - (cl010a cl010)] $ openstack help server create | less

```
Inoltre necessitiamo di avere una rete pronta, che potremo creare così, anche con l'utente cl010u che ha i privilegi più bassi in Openstack:

```console
[user@machine - (cl010u cl010)] $ openstack network create intNetcl010
[user@machine - (cl010u cl010)] $ openstack subnet create --network intnet_cl010 --dhcp \
> --subnet-range 192.168.2.0/24 intnet_subnet_cl010
```
Creiamo ora una macchina, vengono mostrati i comandi per creare sia una fedora cloud sia una macchina con cirros (che è l'opzione consigliata in quanto richiede meno risorse e meno configurazioni):
```console
[user@machine - (cl010u cl010)] $ openstack server create --image cirros --flavor m1.tiny \
> --network intNetcl010 --key-name <nome chiave> cl010_VM0
```
```console
[user@machine - (cl010u cl010)] $ openstack server create --image fedoracloud --flavor m1.small \
> --network intNetcl010 --key-name <nome chiave> cl010_VM1
```

Ad esempio il secondo comando creerà una macchina chiamata cl010_VM1 sul network creato precedentemente (vedasi parte 2 di questo documento), con l'immagine di fedora cloud e con m1.small come flavor.

Fatto questo ci verrà ritornata una tabella con i parametri della VM appena creata e,attendendo qualche secondo, diamo nuovamente il comando **openstack server list** per verificare che la creazione sia andata a buon fine: se tutto è ok la VM1 dovrebbe avere lo status ACTIVE.

Per verificare il corretto funzionamento delle macchine basterà fare SSH nella macchina (il nome utente può essere reperito alla pagina di dowload delle immagini, nel caso di fedora cloud è fedora mentre per cirros vi verranno fornite nella shell di login) o andare sul pannello di controllo nella sezione delle istanze e aprire la tendina della colonna action della VM che ci interessa; per farlo però dobbiamo impostare le chiavi SSH in quanto molte distribuzioni cloud disabilitano di base l'accesso via password.

Per connetterci via SSH necessitiamo dei concetti di networking che vedremo più avanti, per intanto possiamo controllare il corretto boot della macchina tramite i log:
```console
[user@machine - (cl010u cl010)] $ openstack console log show cl010_VM1
```



[continuiamo ora con la parte 4: storage](../4_Storage/Readme.md)
