# Networking

---
**ATTENZIONE**

Per una soluzione grafica fate riferimento al corso CL010 che potete trovare qui: https://www.cl010.it

---

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
Creiamo la subnet:
```console
[user@machine - (cl010u cl010)] $ openstack subnet create subnet1 --network network1 --subnet-range 192.0.2.0/24
```
> N.B.: Con l'opzione --subnet-range 192.0.2.0/24 abbiamo creato l'indirizzamento della nostra rete interna, ricordare che i primi 2 IP della rete sono riservati a Openstack.




