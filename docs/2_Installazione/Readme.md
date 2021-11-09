# Processo d'installazione di un ambiente di laboratorio
### Requirement
I requirement minimi per l'installazione sono una macchina con CPU capace di virtualizzazione, consigliamo 16GB di RAM (possibilmente dedicata, almeno installati) e almeno una scheda di rete; inoltre si consiglia di avere almeno 20GB di spazio disponibile su disco per avere abbastanza spazio di manovra per creare qualche istanza e qualche volume.

### Creazione della VM
### Installazione del laboratorio di testing Openstack all-in-one
Si può installare Openstack utilizzando il repository [RDO](https://www.rdoproject.org/install/packstack/): nel nostro caso installeremo tramite l'applicativo Packstack, che permette di effettuare un'installazione all-in-one, ossia tutto contenuto in una singola macchina; questo setup è ottimo per una fase di testing a livello personale ma non è utilizzabile per un uso enterprise.
Per una RHEL 8 i comandi sono i seguenti:
```console
[user@machine] $ sudo dnf install -y https://www.rdoproject.org/repos/rdo-release.el8.rpm
[user@machine] $ sudo dnf update -y
[user@machine] $ subscription-manager repos --enable codeready-builder-for-rhel-8-x86_64-rpms
[user@machine] $ sudo dnf install -y openstack-packstack
[user@machine] $ sudo packstack --allinone
```

Nel caso voleste utilizzare un diverso sistema operativo o una versione meno recente di RHEL potete andare [sul sito di RDO](https://www.rdoproject.org/install/packstack/) dove troverete tutte le relative informazioni.
