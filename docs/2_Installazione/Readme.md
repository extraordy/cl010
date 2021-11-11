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
[user@host] $ lsmod | grep kvm
kvm_amd               118784  0
kvm                   835584  1 kvm_amd
ccp                   102400  1 kvm_amd
```
Una volta che ci siamo assicurati che la nostra macchina supporti la virtualizzazione e installati il pacchetto di QEMU, scaricheremo un'appropriata ISO da utilizzare: noi di EXTRAORDY consigliamo di provare una macchina con Centos (come verrà mostrato qui in seguito) oppure con RHEL 8.4 (Richiederà una licenza aziendale o, se state testando in proprio e per fini non commerciali, [potete ottenere qui una licenza developer](https://developers.redhat.com/products/rhel/download) ) o con fedora, a seconda delle preferenze di ognuno.

Infine andremo a dare i seguenti comandi per creare ed avviare una macchina virtuale:
```console
[user@host] $ virt-install --virt-type=kvm --name=centosVM --ram 16384 --vcpus 8 --os-variant=rhel8.5 --cdrom=$HOME/Downloads/rhel8-5.iso \
--network=default --graphics vnc --disk size=20 
```
Con ovviamente cdrom la path per la iso sul vostro sistema e disk size di almeno 20GB.

Una volta fatto questo vi verrà aperto un prompt che vi farà connettere alla vostra macchina virtuale su cui, una volta terminata l'installazione, potrete installare il lavoratorio di Openstack.

Se vorrete riaprire più avanti questa macchina potrete utilizzare virsh con i seguenti comandi rispettivamente per visualizzare tutte le macchine installate via QEMU e per avviare la macchina.
```console
[user@host] $ virsh list --all
Id      Name            State
-------------------------------
1       centosVM        shut off
[user@host] $ virsh start centosVM
```
Infine per spegnere il nostro laboratorio basterà dare:
```console
[user@host] $ virsh list
Id      Name            State
-------------------------------
1       centosVM        running

[user@host] $ virsh shutdown centosVM
```
## Installazione del laboratorio di testing Openstack all-in-one
All'interno della nostra macchina di test possiamo dare i seguenti comandi, che ci forniranno l'ambiente di testing; per comodità andremo a disabilitare SELinux e firewalld per maggiore facilità d'utilizzo e cambieremo network manager da NetworkManager a network.
```console
[user@machine] $ sudo dnf install network-scripts -y
[user@machine] $ sudo systemctl disable firewalld
[user@machine] $ sudo systemctl stop firewalld
[user@machine] $ sudo systemctl disable NetworkManager
[user@machine] $ sudo systemctl stop NetworkManager
[user@machine] $ sudo systemctl enable network
[user@machine] $ sudo systemctl start network
[user@machine] $ sudo dnf config-manager --enable powertools
[user@machine] $ sudo dnf install -y centos-release-openstack-victoria
[user@machine] $ sudo dnf update -y
```
Una volta effettuate queste variazioni andremo a effettuare un reboot della macchina e installeremo packstack
```console
[user@machine] $ reboot
[user@machine] $ sudo dnf install -y openstack-packstack
```
Successivamente andremo a verificare che si siano mantenute tutte le impostazioni settate in precedenza:
```console
[user@machine] $ sudo nmcli con show
Error: NetworkManager is not running.
[user@machine] $ sudo getenforce 
Permissive
```
verifichiamo la configurazione di rete su scheda enp1s0:
```console
[user@machine] $ ip a
...output omitted...
2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 56:6f:bc:9a:00:7f brd ff:ff:ff:ff:ff:ff
    inet 172.19.0.241/24 brd 172.19.0.255 scope global enp1s0
       valid_lft forever preferred_lft forever
    inet6 fe80::546f:bcff:fe9a:7f/64 scope link 
       valid_lft forever preferred_lft forever

[user@machine] $ sudo cat ifcfg-enp1s0
TYPE=Ethernet
PROXY_METHOD=none
BROWSER_ONLY=no
BOOTPROTO=none
DEFROUTE=yes
IPV4_FAILURE_FATAL=no
IPV6INIT=yes
IPV6_AUTOCONF=yes
IPV6_DEFROUTE=yes
IPV6_FAILURE_FATAL=no
IPV6_ADDR_GEN_MODE=stable-privacy
NAME=enp1s0
DEVICE=enp1s0
ONBOOT=yes
IPADDR=172.19.0.241
PREFIX=24
GATEWAY=172.19.0.1
DNS1=172.25.0.31
DNS2=8.8.8.8
IPV6_PRIVACY=no
``` 
Andiamo ora a generare un answerfile di default, che è il file che packstack usa per configurare l'installazione di Openstack:
```console
[user@machine] $ sudo packstack --gen-answer-file=packstack-answers.txt.orig
Packstack changed given value  to required value /root/.ssh/id_rsa.pub
Additional information:
 * Parameter CONFIG_NEUTRON_L2_AGENT: You have chosen OVN Neutron backend. Note that this backend does not support the VPNaaS plugin. Geneve will be used as the encapsulation method for tenant networks
```
Generato il file lo andremo a modificare creando packstack-answers_demo_cl010.txt, che sarà la versione usata per questo corso.
Qui sotto sono riportate le differenze tra i due answers file che potete modificare voi stessi con un semplice editor di testo:
```console
[user@machine] $ sudo diff packstack-answers.txt.orig packstack-answers_demo_cl010.txt
323c323
< CONFIG_KEYSTONE_ADMIN_PW=1ac5b07998154d45
---
> CONFIG_KEYSTONE_ADMIN_PW=cl010
909c909
< CONFIG_NEUTRON_OVN_BRIDGE_IFACES=
---
> CONFIG_NEUTRON_OVN_BRIDGE_IFACES=br-ex:enp1s0
1169c1169
< CONFIG_PROVISION_DEMO=y
---
> CONFIG_PROVISION_DEMO=n
```
Le modifiche sono: 
- impostato come password dell'utente admin 'cl010'
- disabilitata la creazione del profilo demo (cosi' non va in conflitto con reti che vogliamo creare)
- impostato mapping tra l'external bridge e la scheda fisica del server (ovviamente modificate a seconda del nome della vostra interfaccia)

infine andremo a eseguire installazione all-in-one (circa 20 minuti su una VM configurata con 24Gb di ram e 4 vcpu)
```console
[user@machine] $ sudo time packstack --answer-file=packstack-answers_demo_cl010.txt
Welcome to the Packstack setup utility

The installation log file is available at: /var/tmp/packstack/20211111-115601-2748fyp2/openstack-setup.log

Installing:
Clean Up                                             [ DONE ]
Discovering ip protocol version                      [ DONE ]
Setting up ssh keys                                  [ DONE ]
...
Preparing Nova VNC Proxy entries                     [ DONE ]
Preparing OpenStack Network-related Nova entries     [ DONE ]
Preparing Nova Common entries                        [ DONE ]
Preparing Neutron API entries                        [ DONE ]
Preparing Neutron L3 entries                         [ DONE ]
Preparing Neutron L2 Agent entries                   [ DONE ]
Preparing Neutron DHCP Agent entries                 [ DONE ]
Preparing Neutron Metering Agent entries             [ DONE ]
Checking if NetworkManager is enabled and running    [ DONE ]
Preparing OpenStack Client entries                   [ DONE ]
Preparing Horizon entries                            [ DONE ]
Preparing Swift builder entries                      [ DONE ]
Preparing Swift proxy entries                        [ DONE ]
Preparing Swift storage entries                      [ DONE ]
Preparing Gnocchi entries                            [ DONE ]
Preparing Redis entries                              [ DONE ]
Preparing Ceilometer entries                         [ DONE ]
Preparing Aodh entries                               [ DONE ]
Preparing Puppet manifests                           [ DONE ]
Copying Puppet modules and manifests                 [ DONE ]
Applying 172.19.0.241_controller.pp
Testing if puppet apply is finished: 172.19.0.241_controller.pp  [ - ]
172.19.0.241_controller.pp:                          [ DONE ]         
Applying 172.19.0.241_network.pp
172.19.0.241_network.pp:                             [ DONE ]      
Applying 172.19.0.241_compute.pp
Testing if puppet apply is finished: 172.19.0.241_compute.pp  [ | ]
172.19.0.241_compute.pp:                             [ DONE ]                  
Applying Puppet manifests                            [ DONE ]                  
Finalizing                                           [ DONE ]                  


 **** Installation completed successfully ******

Additional information:
 * Parameter CONFIG_NEUTRON_L2_AGENT: You have chosen OVN Neutron backend. Note that this backend does not support the VPNaaS plugin. Geneve will be used as the encapsulation method for tenant networks
 * Time synchronization installation was skipped. Please note that unsynchronized time on server instances might be problem for some OpenStack components.
 * File /root/keystonerc_admin has been created on OpenStack client host 172.19.0.241. To use the command line tools you need to source the file.
 * To access the OpenStack Dashboard browse to http://172.19.0.241/dashboard .
Please, find your login credentials stored in the keystonerc_admin in your home directory.
 * The installation log file is available at: /var/tmp/packstack/20211111-115601-2748fyp2/openstack-setup.log
 * The generated manifests are available at: /var/tmp/packstack/20211111-115601-2748fyp2/manifests

real    18m57.316s
user    0m3.780s
sys     0m2.823s
```

dopo l'installazione l'ip del server e' stato associato a br-ex e non piu' a enp1s0
```console
[user@machine] $ sudo ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: enp1s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq master ovs-system state UP group default qlen 1000
    link/ether 56:6f:bc:9a:00:7f brd ff:ff:ff:ff:ff:ff
    inet6 fe80::546f:bcff:fe9a:7f/64 scope link 
       valid_lft forever preferred_lft forever
5: ovs-system: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether 5a:9d:4d:54:ea:d0 brd ff:ff:ff:ff:ff:ff
6: br-ex: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UNKNOWN group default qlen 1000
    link/ether 7e:be:24:56:75:46 brd ff:ff:ff:ff:ff:ff
    inet 172.19.0.241/24 brd 172.19.0.255 scope global br-ex
       valid_lft forever preferred_lft forever
    inet6 fe80::7cbe:24ff:fe56:7546/64 scope link 
       valid_lft forever preferred_lft forever
7: br-int: <BROADCAST,MULTICAST> mtu 1500 qdisc noop state DOWN group default qlen 1000
    link/ether c6:a5:de:3f:d5:1e brd ff:ff:ff:ff:ff:ff
```

Nel virtual switch br-ex confluisce l'interfaccia fisica del server
```console
[user@machine] $ ovs-vsctl show
6bd471a5-22c7-4c37-86a8-c424023c58f9
    Manager "ptcp:6640:127.0.0.1"
        is_connected: true
    Bridge br-ex
        fail_mode: standalone
        Port br-ex
            Interface br-ex
                type: internal
        Port enp1s0
            Interface enp1s0
    Bridge br-int
        fail_mode: secure
        datapath_type: system
        Port br-int
            Interface br-int
                type: internal
    ovs_version: "2.13.6"
```

#### abilitazione della funzionalita' di bash completion
Nel caso vogliate abilitare l'autocompletamento potete farlo con il seguente comando:
```console
[user@machine] $ sudo openstack complete > /etc/bash_completion.d/openstack.bash_completion
The 'openstack bgp speaker show dragents' CLI is deprecated and will be removed in the future. Use 'openstack bgp dragent list' CLI instead.
```
per renderlo attivo basta uscire e rientrare dalla shell, o riavviare la macchina.

### Creazione della network di test
Accediamo ora utilizzando il file keystonerc_admin, del quale verrà data una spiegazione più dettagliata nel capitolo dei comandi base, e creiamo la network all'interno di Openstack:
```console
[user@machine] $ su 
[root@machine] # source /root/keystonerc_admin
[root@machine ~(keystone_admin)] # openstack network create --external --share --provider-network-type flat --provider-physical-network extnet extnet_cl010
+---------------------------+--------------------------------------+
| Field                     | Value                                |
+---------------------------+--------------------------------------+
| admin_state_up            | UP                                   |
| availability_zone_hints   |                                      |
| availability_zones        |                                      |
| created_at                | 2021-11-11T11:29:07Z                 |
| description               |                                      |
| dns_domain                | None                                 |
| id                        | dc3c09bb-6dc2-43e6-b6eb-b892ac7232ae |
| ipv4_address_scope        | None                                 |
| ipv6_address_scope        | None                                 |
| is_default                | False                                |
| is_vlan_transparent       | None                                 |
| mtu                       | 1500                                 |
| name                      | extnet_cl010                         |
| port_security_enabled     | True                                 |
| project_id                | b1ac1fda4cbf4c97906db7c26b5a04e6     |
| provider:network_type     | flat                                 |
| provider:physical_network | extnet                               |
| provider:segmentation_id  | None                                 |
| qos_policy_id             | None                                 |
| revision_number           | 1                                    |
| router:external           | External                             |
| segments                  | None                                 |
| shared                    | True                                 |
| status                    | ACTIVE                               |
| subnets                   |                                      |
| tags                      |                                      |
| updated_at                | 2021-11-11T11:29:08Z                 |
+---------------------------+--------------------------------------+
```
creiamo inoltre una subnet nell'external network
```console
[root@rdo8 ~(keystone_admin)]# openstack subnet create --network extnet_cl010 --no-dhcp --subnet-range 172.19.0.0/24  --allocation-pool start=172.19.0.248,end=172.19.0.252 --dns-nameserver 172.25.0.31 --dns-nameserver 8.8.8.8 extnet_subnet_cl010
+----------------------+--------------------------------------+
| Field                | Value                                |
+----------------------+--------------------------------------+
| allocation_pools     | 172.19.0.248-172.19.0.252            |
| cidr                 | 172.19.0.0/24                        |
| created_at           | 2021-11-11T12:00:06Z                 |
| description          |                                      |
| dns_nameservers      | 172.25.0.31, 8.8.8.8                 |
| dns_publish_fixed_ip | None                                 |
| enable_dhcp          | False                                |
| gateway_ip           | 172.19.0.1                           |
| host_routes          |                                      |
| id                   | 91bae197-845d-40c8-81b1-0b7ba190990f |
| ip_version           | 4                                    |
| ipv6_address_mode    | None                                 |
| ipv6_ra_mode         | None                                 |
| name                 | extnet_subnet_cl010                  |
| network_id           | dc3c09bb-6dc2-43e6-b6eb-b892ac7232ae |
| prefix_length        | None                                 |
| project_id           | b1ac1fda4cbf4c97906db7c26b5a04e6     |
| revision_number      | 0                                    |
| segment_id           | None                                 |
| service_types        |                                      |
| subnetpool_id        | None                                 |
| tags                 |                                      |
| updated_at           | 2021-11-11T12:00:06Z                 |
+----------------------+--------------------------------------+
```
> ATTENZIONE!
> quella qui sopra è' la rete fisica su cui vive il server dove ho installato openstack.
> Pertanto dovrete modificare il range dell'allocation pool in modo che non vada in conflitto con altri server gia' presenti sulla rete fisica, nell'esempio abbiamo impostato partendo da .248 fino ad arrivare a .252.

[continuiamo ora con la parte 3: comandi base](../3_Comandi_base/Readme.md)

