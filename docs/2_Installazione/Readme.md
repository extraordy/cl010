# Processo d'installazione di un ambiente di laboratorio
### Requirement
I requirement minimi per l'installazione sono una macchina con CPU capace di virtualizzazione, consigliamo 16GB di RAM (possibilmente dedicata, almeno installati) e almeno una scheda di rete; inoltre si consiglia di avere almeno 20GB di spazio disponibile su disco per avere abbastanza spazio di manovra per creare qualche istanza e qualche volume.

### Creazione della VM
Per avere un ambiente di testing facilmente resettabile e riutilizzabile, installeremo il nostro laboratorio di test all'interno di una virtual machine.

Se non lo avete ancora fatto potete seguire [il nostro corso sulla virtualizzazione qui](https://rh018.it/) per avere tutti i dettagli più interessanti sulle varie tecnologie di virtualizzazione e orchestrazione di macchine virtuali come oVirt (discuteremo anche nella prossima sezione della differenza tra un'istanza Openstack e una macchina virtuale).

Per la creazione della macchina virtuale utilizzeremo QEMU (Quick EMUlator) ma se preferite utilizzare qualunque altro software di virtualizzazione il risultato finale non differirà.

Prima di tutto occorre controllare che la CPU supporti la virtualizzazione: per virtualizzare il kernel Linux utilizza i moduli kvm (kvm.ko, kvm-intel.ko, kvm-amd.ko) che sono generalmente caricati dal kernel automaticamente se la CPU espone le funzionalità di virtualizzazione (Intel VT / AMD-V).

Per confermare che i moduli siano attivi, si può fare un lsmod:
```console
[user@machine] $ lsmod | grep kvm
kvm_amd               118784  0
kvm                   835584  1 kvm_amd
ccp                   102400  1 kvm_amd
```
Una volta che ci siamo assicurati che la nostra macchina supporti la virtualizzazione e installati il pacchetto di QEMU, scaricheremo un'appropriata ISO da utilizzare: noi di EXTRAORDY consigliamo di provare una macchina con RHEL 8.4 (Richiederà una licenza aziendale o, se state testando in proprio e per fini non commerciali, [potete ottenere qui una licenza developer](https://developers.redhat.com/products/rhel/download) ) o con una derivata di RHEL, come centos o fedora, a seconda delle preferenze di ognuno.

Infine andremo a dare i seguenti comandi per creare ed avviare una macchina virtuale:
```console
[user@machine] $ virt-install --virt-type=kvm --name=rhelVM --ram 4096 --vcpus 2 --os-variant=rhel8.5 --cdrom=$HOME/Downloads/rhel8-5.iso \
--network=default --graphics vnc --disk size=20 
```
Con ovviamente cdrom la path per la iso sul vostro sistema e disk size di almeno 20GB.

Una volta fatto questo vi verrà aperto un prompt che vi farà connettere alla vostra macchina virtuale su cui, una volta terminata l'installazione, potrete installare il lavoratorio di Openstack.

Se vorrete riaprire più avanti questa macchina potrete utilizzare virsh con i seguenti comandi rispettivamente per visualizzare tutte le macchine installate via QEMU e per avviare la macchina.
```console
[user@machine] $ virsh list --all
Id      Name            State
-------------------------------
1       rhelVM        shut off
[user@machine] $ virsh start rhelVM
```
Infine per spegnere il nostro laboratorio basterà dare:
```console
[user@machine] $ virsh list
Id      Name            State
-------------------------------
1       rhelVM        running

[user@machine] $ virsh shutdown rhelVM
```
### Installazione del laboratorio di testing Openstack all-in-one
Il nostro ambiente di environment verrà installato tramite [RDO](https://www.rdoproject.org/install/packstack/), un repository che contiene al suo interno Packstack.

Packstack è un applicativo che permette di effettuare un'installazione all-in-one, ossia tutto contenuto in una singola macchina; questo setup è ottimo per un ambiente di laboratorio a livello personale ma non è utilizzabile per un uso enterprise.
Per una RHEL 8 i comandi sono i seguenti:
```console
[user@machine] $ sudo dnf install -y https://www.rdoproject.org/repos/rdo-release.el8.rpm
[user@machine] $ sudo dnf update -y
[user@machine] $ subscription-manager repos --enable codeready-builder-for-rhel-8-x86_64-rpms
[user@machine] $ sudo dnf install -y openstack-packstack
[user@machine] $ sudo packstack --allinone
```

Nel caso voleste utilizzare un diverso sistema operativo o una versione meno recente di RHEL potete andare [sul sito di RDO](https://www.rdoproject.org/install/packstack/) dove troverete tutte le relative informazioni.

Il processo di installazione è lungo e deve necessariamente effettuare alcuni controlli, pertanto se vedete che la vostra macchina è bloccata nel controllare alcune impostazioni di puppet attendete, è normale.
