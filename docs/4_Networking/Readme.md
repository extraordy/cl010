# Networking in Openstack

##Le reti Software Defined
Il servizio netwoking di Openstack fornisce un sistema scalabile per la gestione della connettività di rete all'interno di un'implementazione cloud OpenStack. Può reagire facilmente e rapidamente alle mutevoli esigenze della rete (ad esempio, creando e assegnando nuovi indirizzi IP). 

###Creazione di una Network

Accedi alla **dashboard.

Seleziona il **Project appropriato dal menu a tendina in alto a sinistra.
Nella scheda **Progetto, apri la scheda **Network e fai clic sulla categoria Network.

Fare clic su **Create **Networt.
Nella finestra di dialogo **Create **Network, specificare i seguenti valori;
Network Name: specificare un nome per identificare la rete.
Shared: condividi la rete con altri progetti.
Admin State: lo stato in cui avviare la rete.
Create Subnet: seleziona questa casella di controllo per creare una sottorete

N.B,: Non è necessario specificare una sottorete quando crei una rete, ma se non specifichi una sottorete, la rete non può essere collegata a un'istanza. 
