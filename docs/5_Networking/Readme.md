# Networking

## Le reti Software Defined

Il servizio networking di Openstack fornisce un sistema scalabile per la gestione della connettività di rete all'interno di un'implementazione cloud OpenStack. Può reagire facilmente e rapidamente alle mutevoli esigenze della rete (ad esempio, creando e assegnando nuovi indirizzi IP). 

## Creazione di una Network

### Esercizio
**Creiamo un rete chiamata network1**
### Soluzione
Cominciamo con il creare la rete:

```console
[user@machine - (cl010u cl010)] $ openstack network create network1
```

## Creazione di una Subnet

### Esercizio
**Creiamo un subnet chiamata subnet1**
### Soluzione
Creiamo la subnet e colleghiamo alla network :
```console
[user@machine - (cl010u cl010)] $ openstack subnet create subnet1 --network network1 --subnet-range 192.0.2.0/24
```
> N.B.: Con l'opzione --subnet-range 192.0.2.0/24 abbiamo creato l'indirizzamento della nostra rete interna, ricordare che i primi 2 IP della rete sono riservati a Openstack.

A questo abbiamo creato la nostra internal network, per abilitarla al traffico esterno è necessario proseguire con la creazione del router.

# Creazione di una Router

### Esercizio
**Creiamo un router chiamato router1**
### Soluzione
Andiamo a creare il router:
```console
[user@machine - (cl010u cl010)] $ openstack router create router1
```
Ora è possibile agganciare il router alla rete:

```console
[user@machine - (cl010u cl010)] $ openstack router add subnet router1 subnet1
```
> N.B.: E' necessario precedentemente a questo step, aver creato una external network. 
Infine sarà possibile aggiungere il router alla rete esterna:
```console
[user@machine - (cl010u cl010)] $ openstack router set router1 --external-gateway id_external_network
``` 
> > N.B.: Ricorda che è necessario utilizzare una corretta configurazione dei security group abilitando il protocollo SSH per accedere alla vm.

# Attach di un Foating IP 
Se un pool di indirizzi IP, configurato dall'amministratore cloud, è disponibile in OpenStack, lo possiamo aggiungere alla nostra istanza:

## Esercizio
**Stacchiamo un Floating* da pool*
### Soluzione

```console
[user@machine - (cl010u cl010)]openstack floating ip create pool_ip_name
```
A questo punto, recuperando il floating IP che abbiamo appena creato e l'ID del server, possiamo lanciare il comando:

```console
[user@machine - (cl010u cl010)] $ openstack server add floating ip id_server FLOATING_IP
```
Per rimuovere il Floating IP sarà sufficente:

 ```console
[user@machine - (cl010u cl010)] $openstack server remove floating ip id_server FLOATING_IP
```
In questo modo la nostra istanza non sarà più raggiungibile dall'eterno, ma il Floating IP rimane disponibile nel pool e pronto per essere riassociato.




