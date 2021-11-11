# Introduzione a Openstack

## Cos'è openstack?
Openstack è la tecnologia sviluppata anche da Red Hat e originariamente creata dalla **NASA** e da **Rackspace** con l'obiettivo finale di creare un sistema completamente open source in grado di deployare un proprio cloud su un parco macchine aziendale, o come viene solitamente indicato IaaS (Infrastructure as a service). <br>
La forza di Openstack è la sua possibilità di garantirci varie tipologie di deploy per il nostro cloud, ossia Public, private e hybrid.<br>
Ad oggi molte aziende supportano attivamente Openstack, ricordiamo qui alcuni memberi come Red Hat, AT&T, Ericsson, Canonical, Dell, Cisco, VMware, Rackspace e OVHcloud.

## Perché proprio Openstack?
* **Management interno all'azienda**
    L'infrastruttura IaaS permette di scalare con estrema facilità le risorse assegnate alle varie macchine senza bisogno di richiedere ad esterni o pagare per spostare risorse tra macchine etc..
    La scalabilità inoltre può essere regolata per seguire il picco di traffico sui propri server, garantendo così il miglior servizio possibile senza alcuno spreco di risorse e, soprattutto, senza spese aggiuntive.
* **Automation**
    Openstack offre moltissime opzioni per automatizzare task tramite API, rendendo il lavoro del sistemista che lo mantiene più semplice e liberando tempo per compiti più importanti.
* **Creazione di expertise interno**
    Ovviamente l'adozione di una tecnologia come Openstack permette di creare expertise dal grande valore commerciale all'interno dell'azienda, evitando di dipendere da tool o aziende esterne per l'infrastruttura aziendale.
    Inoltre fornisce la possibilità futura di crescita aggiungendo anche la possiblità di offrire questo servizio ai futuri clienti della vostra azienda.
* **Openstack Cloud**
    Nel caso foste esclusivamente interessati a Openstack ma non a tutti i grattacapi che porta doverlo configurare e mantenere, esiste la possibilità di ottenerne uno in Cloud!
    Questo permetterà a voi di non dover pensare alle configurazioni e ad un prezzo estemamente competitivo avrete a disposizione un istanza cloud di Openstack, con tutti i vantaggi del caso e nessun bisogno di avere Hardware in azienda.
    A questo link è possibile trovare il motore di ricerca di Openstack per immagini che vengono supportate in un ambienta cloud
* **Community driven**
    Come tutti i progetti Open source anche Openstack beneficia enormemente dal supporto di una grossa community di professionisti capaci e formati, che aiutano il progetto a crescere e migliorare ogni giorno di più, oltre ad ovviamente il supporto offerto da Red Hat e i Red Hat partner come EXTRAORDY, nei corsi di formazione come il CL110 e CL210 (Openstack Adminsitrator I e II).

## Quali sono i componenti di Openstack?
Openstack è composto da vari moduli, che si occupano di gestire tutti i compiti del sistema: Openstack infatti è un wrapper python il cui compito originario era coordinare tutte queste tecnologie in un'unica soluzione più comoda da utilizzare; è comunque rimasta la possibilità di usare i singoli componenti.
Qui di sotto verranno introdotti alcuni dei più famosi
- **Nova**
    Nova è la parte del progetto Openstack che fornisce il modo di lanciare istanze (ossia i nostri server virtuali), utilizzando tutti gli altri tool per compiere le varie operazioni necessarie.
- **Cinder**:
    Cinder è uno dei componenti di Openstack che si occupa della gestione di storage, in modo particolare di block storage, che fornisce all'utente finale una semplice API per richiedere e consumare spazio di memoria senza la diretta conoscenza da parte dell'utente di tutti i dettagli sottostanti.
- **Neutron** :
    Neutron è il componente che fornisce funzionalità di SDN (Software Defined Network), ossia permette di creare reti software facilmente gestibili dall'utente amministratore nascondendo sotto di sè tutta la complessità della reindirizzazione dei pacchetti e della gestione dinamica di reti in un ambiente che cambia dinamicamente come quello di Openstack.
- **Keystone**:
    Keystone è il componente responsabile per la scoperta dei servizi Openstack, fornisce le API per la client authentication e le distributed multi-tenanti authorization.
    Si occupa inoltre di fornire il servizio Resource, che si occupa di fornire dati su progetti e domini.
- **Glance** :
    Glance è il componente che svolge il compito di Image Registry, ossia cataloga, eroga e gestisce le immagini che andremo ad utilizzare nel nostro sistema; la sua potenza deriva anche dal fatto che è possibile utilizzarlo per fare query sui metadati delle immagini e non solo sulle immagini stesse, garantendo così un'ancora più ampia flessibilità.

[Se siete interessati qui potrete trovare tutti gli altri componenti.](https://www.openstack.org/software/project-navigator/openstack-components#openstack-services)

[Passiamo ora alla parte di installazione della VM e di setup di Openstack in ambiente virtualizzato](../2_Installazione/Readme.md)


