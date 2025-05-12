ZD-R1(config)#enable secret pwdpriv
ZD-R1(config)#no ip domain lookup
ZD-R1(config)#banner motd #message#


ZD-R1(config)#line console 0
ZD-R1(config-line)#password pwdconsole
ZD-R1(config-line)#login 
ZD-R1(config-line)#exit

ZD-R1(config)#service password-encryption

ZD-R1(config)#int g0/0 
ZD-R1(config-if)#ip address 172.16.1.1 255.255.255.0 
ZD-R1(config-if)#no shut



## Fascicule 2

ZA-R1(config)#ip route 172.16.50.0 255.255.255.0 S0/0/0

ZA-R1(config)#ip route 172.16.51.0 255.255.255.0 10.6.6.2 (troncon)
ZA-R2(config)#ip route 0.0.0.0 0.0.0.0 10.6.6.1 (backbone)


ZA-R2(config)#int S0/0/1
ZA-R2(config-if)#ip address 10.8.8.1 255.255.255.252
ZA-R2(config-if)#do wr


ZA-R2(config)#ip route 20.30.10.0 255.255.255.252 10.8.8.2 5  distance admin 5


## Fascicule 3

### protocole RIPv2 sur les routeurs de cette
ZC-R1(config)#router rip
ZC-R1(config-router)#version 2
ZC-R1(config-router)#network 10.0.0.0
ZC-R1(config-router)#network 10.0.0.0
ZC-R1(config-router)#do wr

#### Désactivez l’envoi inutile des message
ZC-R3(config)#router rip
ZC-R3(config-router)#version 2
ZC-R3(config-router)#passive-interface g0/0
ZC-R3(config-router)#do wr


####  désactiver la récapitulation automatique
ZC-R3(config)#router rip
ZC-R3(config-router)#version 2
ZC-R3(config-router)#no auto-summary
ZC-R3(config-router)#do wr

#### permet la propagation de la route statique par défaut
ZC-R1(config)#router rip
ZC-R1(config-router)#version 2
ZC-R1(config-router)#default-information originate


show ip protocols
### Fascicule 4

##### Activer ospf 
router ospf 10
network 172.16.60.0 0.0.0.3 area 1
network 172.16.61.0 0.0.0.3 area 1

#### optimiser l’envoi des mises à jour sur les interfaces inutiles
ZB-R2(config)#router ospf 10
ZB-R2(config-router)#passive-interface g0/0
ZB-R2(config-router)#end


show ip ospf neighbor

##### Appliquer la 1ere regle 
ZB-R2(config)#router ospf 10
ZB-R2(config-router)#router-id 2.2.2.2

##### appliquer la 2 ème règle sur le router ZB-R1.
ZB-R1(config)#interface loopback 0 ZB-R1(config-if)#ip address 172.16.50.1 255.255.255.0

##### vérifier la valeur de la bande passante d’une interface ?
show int s0/0/0 | include BW

##### set bandwidth 
ZB-R2(config)#int s0/0/0
ZB-R2(config-if)#bandwidth 128

##### set cout 

ZB-R1(config)#int s0/0/0
ZB-R1(config-if)#ip ospf cost 1

##### solution à ce problème ?
ZB-R1(config)#router ospf 10
ZB-R1(config-router)#default-information originate
ZB-R1(config-router)#end


### Fascicule 5
##### configuration du protocole OSPFv2
Backbone-Router1(config)#router ospf 100
Backbone-Router1(config-router)#network 20.30.20.1 0.0.0.3 area 0
Backbone-Router1(config-router)#network 20.30.10.1 0.0.0.3 area 0
Backbone-Router1(config-router)#network 20.20.23.1 0.0.0.255 area 0


##### changer  la prio
Backbone-Router1(config)#int g0/0
Backbone-Router1(config-if)#ip ospf prio 250


##### Configurez les interfaces du routeur Backbone-Router4 avec les adresses IP
Router(config)#int g0/0
Router(config-if)#ip address 20.20.23.4 255.255.255.0
Router(config-if)#no shut


#### Fascicule 6 
Un mappage statique doit être configuré sur le routeur ZB-R1

ZB-R1(config)#ip nat inside source static 172.16.64.10 30.30.30.129.



ZB-R1(config)#int S0/0/0
ZB-R1(config-if)#ip nat inside
ZB-R1(config-if)#int S0/0/01
ZB-R1(config-if)#ip nat inside
ZB-R1(config-if)#int S0/1/1
ZB-R1(config-if)#ip nat ouside

##### translation
ZB-R1#show ip nat translations
##### contrôle d’accès
ZC-R1(config)#access-list 1 permit 172.16.40.0 0.0.0.255
##### Définissez un pool d’adresses publiques
ZC-R1(config)#ip nat pool NAT2-GROUP6-ZC 30.30.30.65 30.30.30.65 netmask 0.0.0.192

##### Associez le pool d’adresses publiques

ZC-R1(config)#ip nat inside source list 1 pool NAT-GROUPE6-ZC
