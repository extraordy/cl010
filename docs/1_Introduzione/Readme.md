# Introduzione a Openstack

## Cos'è openstack?
Openstack è la tecnologia sviluppata attivamente da Red Hat e originariamente creata dalla **NASA** con l'obiettivo finale di creare un sistema completamente open source in grado di deployare un proprio cloud su un parco macchine aziendale, o come viene solitamente indicato IaaS (Infrastructure as a service).
La forza di Openstack è la sua possibilità di garantirci varie tipologie di deploy per il nostro cloud, ossia Public, private e hybrid.
Ad oggi ben 500 aziende  (tra cui red Hat, Oracle, IBM e molti altri big del settore) supportano attivamente Openstack.

### Cloud: esistono differenze?
_Si!_ <br>
La differenza più grande che possiamo vedere è quella di utilizzo, ossia per quanto Openstack sia potente rimane comunque un coltellino svizzero,

### Vantaggi del cloud privato
* **Creazione di expertise interno**
    Ovviamente l'adozione di una tecnologia come Openstack permette di creare expertise dal grande valore commerciale all'interno dell'azienda, evitando di dipendere da tool o aziende esterne per l'infrastruttura aziendale.
    Inoltre fornisce la possibilità futura di crescita aggiungendo anche la possiblità di offrire questo servizio ai futuri clienti della vostra azienda.

* ** Openstack Cloud **
    Nel caso foste esclusivamente interessati a Openstack ma non a tutti i grattacapi che porta doverlo configurare e mantenere, esiste la possibilità di ottenerne uno in Cloud!
    Questo permetterà a voi di non dover pensare alle configurazioni e ad un prezzo estemamente competitivo avrete a disposizione un istanza cloud di Openstack, con tutti i vantaggi del caso e nessun bisogno di avere Hardware in azienda.
* **Management interno all'azienda**
    L'infrastruttura IaaS permette di scalare con estrema facilità le risorse assegnate alle varie macchine senza bisogno di richiedere ad esterni o pagare per spostare risorse tra macchine etc..
    La scalabilità inoltre può essere regolata per seguire il picco di traffico sui propri server, garantendo così il miglior servizio possibile senza alcuno spreco di risorse e, soprattutto, senza spese aggiuntive.
* **Automation**
    Openstack offre moltissime opzioni per automatizzare task tramite API, rendendo il lavoro del sistemista che lo mantiene più semplice e liberando tempo per compiti più importanti.
* **Community driven**
    Come tutti i progetti Open source anche Openstack beneficia enormemente dal supporto di una grossa community di professionisti capaci e formati, che aiutano il progetto a crescere e migliorare ogni giorno di più, oltre ad ovviamente il supporto offerto da Red Hat e i Red Hat partner come EXTRAORDY, nei corsi di formazione come il CL110 e CL210 (Openstack Adminsitrator I e II).
### Perché proprio openstack?
- primo
- secondo

## Come possiamo installare Openstack?
Si può installare Openstack utilizzando il repository [RDO](https://www.rdoproject.org/install/packstack/), tramite l'applicativo Packstack, che permette di effettuare un'installazione all-in-one in maniera semplice ed efficente.
Per una RHEL 8 i comandi sono i seguenti:
```console
[user@machine] $ sudo dnf install -y https://www.rdoproject.org/repos/rdo-release.el8.rpm
[user@machine] $ sudo dnf update -y
[user@machine] $ subscription-manager repos --enable codeready-builder-for-rhel-8-x86_64-rpms
[user@machine] $ sudo dnf install -y openstack-packstack
[user@machine] $ sudo packstack --allinone
```

I requirement minimi per l'installazione sono una macchina con CPU capace di virtualizzazione, 16GB di RAM (possibilmente dedicata, almeno installati) e almeno una scheda di rete.
