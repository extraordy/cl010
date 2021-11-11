# Introduzione allo storage con cinder

> **ATTENZIONE** <br>
> Per una soluzione grafica fate riferimento al corso CL010 che potete trovare qui: https://www.cl010.it <br>
> Inoltre per favorire la leggibilità non tutti gli output a schermo verranno mostrati.

---

## Parte 0: Creazione di un volume con Openstack

I volumi sono dispositivi di archiviazione a blocchi collegati alle istanze. Puoi collegare un volume a un'istanza in esecuzione o scollegare un volume e collegarlo a un'altra istanza in qualsiasi momento.

### Esercizio
**Creiamo un volume di dimensione 8GB chiamato my-new-volume**
### Soluzione
Cominciamo con il creare un volume :

```console
[user@machine - (cl010u cl010)] $ openstack volume create --size 8 my-new-volume 
```

Successivamente è possibile proseguire verificando il volume creato:
```console
[user@machine - (cl010u cl010)] $ openstack volume list
+--------------------------------------+---------------+-----------+------+-------------+
| ID                                   | Name          |  Status   | Size | Attached to |
+--------------------------------------+---------------+-----------+------+-------------+
| bab4b0e0-ce3d-4d57-bf57-3c51319f5202 | my-new-volume | available | 8    |             |
+--------------------------------------+---------------+-----------+------+-------------+
```

## Parte 1: Attach di un volume
### Esercizio
**Agganciamo il volume creato al punto precedente ad un'istanza creata in precedenza**
### Soluzione
Per agganciare un volume ad un'istanza su Openstack lanciamo il comando:

```console
[user@machine - (cl010u cl010)] $ openstack server add volume id_ volume id_istanza --device /dev/sdb
```

> N.B.: Per recuperare l'id_istanza è possibile lanciare il comando **openstack server list**, il --device indica l'assegnazione del disco sull'instanza.

Il volume ora risulta "**in-use**" collegato alla nostra istanza, per verificare:
```console
[user@machine - (cl010u cl010)] $ openstack volume list
+--------------------------------------+---------------+-----------+------+-------------------------------------+
| ID                                   | Name          |  Status   | Size | Attached to                         |
+--------------------------------------+---------------+-----------+------+-------------------------------------+
| bab4b0e0-ce3d-4d57-bf57-3c51319f5202 | my-new-volume |  in-use   | 8    | Attacced to my_instance on /dev/vdb |
+--------------------------------------+---------------+-----------+------+-------------------------------------+
```

Una volta agganciato il volume e dopo aver creato il filesystem, è possibile montarlo collegandosi in ssh o direttamente dalla console della dashboard:

```console
[user@machine - (cl010u cl010)] $ mkfs -t ext4 /dev/vdb
[user@machine - (cl010u cl010)] $ mkdir /vdb
[user@machine - (cl010u cl010)] $ mount -t ext4 /dev/vdb /vdb
```

## Parte 2: Detach di un volume
### Esercizio
**Eseguiamo un detach del volume agganciato ad un'istanza nel punto precedente**
### Soluzione
E' possibile effetture il detach del volume, prima assicuratevi di aver smontato il disco dal mountpoint precedentemente creato:

```console
[user@machine - (cl010u cl010)] $ openstack server remove volume  id_volume id_istanza
```
A questo punto il volume è stato smontato dall'istanza ma è ancora presente su Cinder, non ci resta che proseguire con l'eliminazione.

## Parte 3: Eliminazione del volume
### Esercizio
**Cancelliamo infine il volume creato**
### Soluzione
E' possibile infine rimuovere il volume che abbiamo creato all'inizio con il comando:
```console
[user@machine - (cl010u cl010)] $ openstack volume delete my-new-volume
```

[Infine andiamo alla parte finale sul networking](../5_Networking/Readme.md)
