# Introduzione allo storage con cinder

## Parte 0: Creazione di un volume 
### Esercizio
Per creare un volume su Openstack lanciamo il comando:

```console
[user@machine - (cl010u cl010)] openstack volume create --size 8 my-new-volume 
```

Una volta creato il volume lanciamo il comando:
```console
[user@machine - (cl010u cl010)]openstack volume list
+--------------------------------------+---------------+-----------+------+-------------+
| ID                                   | Name          |  Status   | Size | Attached to |
+--------------------------------------+---------------+-----------+------+-------------+
| bab4b0e0-ce3d-4d57-bf57-3c51319f5202 | my-new-volume | available | 8    |             |
+--------------------------------------+---------------+-----------+------+-------------+
```

## Parte 1: Attach di un volume
### Esercizio
Per attaccare un volume su ad un istanza su Openstack lanciamo il comando:

```console
[user@machine - (cl010u cl010)] openstack server add volume id_ volume id_istanza --device /dev/sdb
```

> N.B.: Per recuperare l'id_istanza è possibile lanciare il comando openstack server list, il --device indica l'assegnazione del disco sull'instanza.


Una volta attaccato il volume all'istanza e dopo aver creato un filesystem, è possibile montarlo collegando in ssh sulla nostra istanza o direttamente dalla console dell'istanza
su openstack:

```console
mkfs -t ext4 /dev/vdb
mkdir /vdb
mount -t ext4 /dev/vdb /vdb
```

## Parte 2: Detach di un volume
### Esercizio
E' possibile effetture il detach del volume, prima assicuratevi di aver smontato il disco dal mountpoint sull'istanza precedentemente creato:

```console
[user@machine - (cl010u cl010)]openstack server remove volume volume id_ volume id_istanza
```
A questo punto il volume è stato smontato dell'istanza ma è ancora presente su Openstack, non ci resta che proseguire con l'eliminazione.

## Parte 4: Eliminazione del volume
### Esercizio
E' possibile infine rimuovere il volume che abbiamo creato all'inizio con il comando:
```console
[user@machine - (cl010u cl010)]openstack volume delete my-new-volume
```
