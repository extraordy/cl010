# Networking

> **ATTENZIONE** <br>
> Per una soluzione grafica fate riferimento al corso CL010 che potete trovare qui: https://www.cl010.it <br>
> Inoltre per favorire la leggibilità non tutti gli output a schermo verranno mostrati.

---

## Le reti Software Defined

Il servizio networking di Openstack fornisce un sistema scalabile per la gestione della connettività di rete all'interno di un'implementazione cloud OpenStack. Può reagire facilmente e rapidamente alle mutevoli esigenze della rete (ad esempio, creando e assegnando nuovi indirizzi IP). 

## Creazione di una Network

### Esercizio
**Creiamo un rete chiamata network1**
### Soluzione
Cominciamo con il creare la rete:

```console
[user@machine - (cl010u cl010p)] $ openstack network create network1
```

## Creazione di una Subnet

### Esercizio
**Creiamo un subnet chiamata subnet1**
### Soluzione
Creiamo la subnet e colleghiamo alla network :
```console
[user@machine - (cl010u cl010p)] $ openstack subnet create subnet1 --network network1 --subnet-range 192.0.2.0/24
```
> N.B.: Con l'opzione --subnet-range 192.0.2.0/24 abbiamo creato l'indirizzamento della nostra rete interna, ricordare che i primi 2 IP della rete sono riservati a Openstack.

A questo abbiamo creato la nostra internal network, per abilitarla al traffico esterno è necessario proseguire con la creazione del router.

# Creazione di un Router

### Esercizio
**Creiamo un router chiamato router1**
### Soluzione
Andiamo a creare il router:
```console
[user@machine - (cl010u cl010p)] $ openstack router create router1
```
Ora è possibile agganciare il router alla rete:

```console
[user@machine - (cl010u cl010p)] $ openstack router add subnet router1 subnet1
```
> N.B.: E' necessario precedentemente a questo step, aver creato una external network che noi abbiamo configurato nell'installazione. 
Infine sarà possibile aggiungere il router alla rete esterna:
```console
[user@machine - (cl010u cl010p)] $ openstack router set router1 --external-gateway extnet_cl010
``` 

# Attach di un Foating IP 
Se un pool di indirizzi IP, configurato dall'amministratore cloud, è disponibile in OpenStack, lo possiamo aggiungere alla nostra istanza:

## Esercizio
**Creiamo, allochiamo e deallochiamo un Floating da pool**
### Soluzione

```console
[user@machine - (cl010u cl010p)] $ openstack floating ip create extnet_cl010
```

A questo punto, recuperando il floating IP che abbiamo appena creato e l'ID del server, possiamo lanciare il comando:

```console
[user@machine - (cl010u cl010p)] $ openstack server add floating ip id_server FLOATING_IP
```
Per rimuovere il Floating IP sarà sufficente:

 ```console
[user@machine - (cl010u cl010p)] $openstack server remove floating ip id_server FLOATING_IP
```
In questo modo la nostra istanza non sarà più raggiungibile dall'esterno, ma il Floating IP rimane disponibile nel pool e pronto per essere riassociato.

Per deallocare il Floating ip dal pool:

```console
[user@machine - (cl010u cl010p)] $ openstack floating ip delete FLOATING_IP
```



