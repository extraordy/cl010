# Networking

## Le reti Software Defined

Il servizio netwoking di Openstack fornisce un sistema scalabile per la gestione della connettività di rete all'interno di un'implementazione cloud OpenStack. Può reagire facilmente e rapidamente alle mutevoli esigenze della rete (ad esempio, creando e assegnando nuovi indirizzi IP). 

## Creazione di una Network

### Esercizio
Accedi alla **dashboard**.

Seleziona il **Project** appropriato dal menu a tendina in alto a sinistra.
Nella scheda **Project**, apri la scheda **Network** e fai clic sulla categoria Network.

Fare clic su **Create** **Network**.
Nella finestra di dialogo **Create** **Network,** specificare i seguenti valori;
Network Name: specificare un nome per identificare la rete.
Shared: condividi la rete con altri progetti.
Admin State: lo stato in cui avviare la rete.
Create Subnet: seleziona questa casella di controllo per creare una sottorete

>N.B,: Non è necessario specificare una sottorete quando crei una rete, ma se non specifichi una sottorete, la rete non può essere collegata a un'istanza. 

## Creazione di una Subnet

### Esercizio

Subnet Name: specificare un nome per la sottorete.

Network Address: specificare l'indirizzo IP per la sottorete.

IP Version: selezionare IPv4 o IPv6.

Gateway IP: specificare un indirizzo IP per un gateway specifico. Questo parametro è facoltativo.

Disable Gateway: selezionare questa casella di controllo per disabilitare un indirizzo IP del gateway.

Details sottorete:

Enable DHCP: selezionare questa casella di controllo per abilitare DHCP.

Allocation Pools: specificare i pool di indirizzi IP.

DNS Name Servers: specificare un nome per il server DNS.

Route host: specificare l'indirizzo IP delle route host. 

Clicca Crea.


