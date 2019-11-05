# TP3 : Routage INTER-VLAN + mise en situation

# I. *Router-on-a-stick*


**Tableau des réseaux utilisés**

Réseau | Adresse | VLAN | Description
--- | --- | --- | ---
`net1` | `10.3.10.0/24` | 10 | Utilisateurs
`net2` | `10.3.20.0/24` | 20 | Admins
`net3` | `10.3.30.0/24` | 30 | Visiteurs
`netP` | `10.3.40.0/24` | 40 | Imprimantes

**Tableau d'adressage**

Machine | VLAN | IP `net1` | IP `net2` | IP `net3` |  IP `netP`
--- | --- | --- | --- | --- | ---
PC1 | 10 | `10.3.10.1/24` | x | x | x
PC2 | 20 | x | `10.3.20.2/24` | x | x | x
PC3 | 20 | x | `10.3.20.3/24` | x | x | x
PC4 | 30 | x | x |  `10.3.30.4/24` | x | x
P1 | 40 | x | x | x | `10.3.40.1/24` 
R1 | x |  `10.3.10.254/24` | `10.3.20.254/24` | `10.3.30.254/24` | `10.3.40.254/24` 

**Qui peut joindre qui ?**

✅ = Peuvent se joindre  
❌ = Ne peuvent pas se joindre

Réseaux | `net1` |  `net2` |  `net3` |  `netP`
--- | --- | --- | --- | ---
 `net1` | ✅ | ❌ | ❌ | ❌
 `net2` | ❌ | ✅ | ✅ | ✅
 `net3` | ❌ | ✅ | ✅ | ✅
 `netP` | ❌ | ✅ | ✅ | ✅

* Vérifions la communication des machines entres elles
    * Sur PC 1 :
        * Utilisez la commande suivante :
            * `ping 10.3.20.2`
            * `ping 10.3.20.3`
            * `ping 10.3.30.4`
            * `ping 10.3.40.1`
    * Sur PC 2 :
        * Utilisez la commande suivante :
            * `ping 10.3.10.1`
            * `ping 10.3.20.3`
            * `ping 10.3.30.4`
            * `ping 10.3.40.1`
    * Sur PC 3 :
        * Utilisez la commande suivante :
            * `ping 10.3.10.1`
            * `ping 10.3.20.2`
            * `ping 10.3.30.4`
            * `ping 10.3.40.1`
    * Sur PC 4 :
        * Utilisez la commande suivante :
            * `ping 10.3.10.1`
            * `ping 10.3.20.2`
            * `ping 10.3.30.3`
            * `ping 10.3.40.1`

    * On peut s'apercevoir que :
        * PC 1 ne peux communiquer avec personne
        * PC 2 peux communiquer avec PC 3, PC 4 et P1
        * PC 3 peux communiquer avec PC 2, PC 4 et P1
        * PC 4 peux communiquer avec PC 2, PC 3 et P1

## II. Cas concret

* Configuration des sous-interfaces de notre router :

```
R1#conf t
R1(config)#int eth0/1
R1(config-if)#no shut
R1(config-if)#exit
R1(config)#int eth0/1.10
R1(config-subif)#encapsulation dot1q 10
R1(config-subif)#ip address 10.3.10.254 255.255.255.192
R1(config-subif)#no shut
R1(config-subif)#exit
R1(config)#int eth0/1.20
R1(config-subif)#encapsulation dot1q 20
R1(config-subif)#ip address 10.3.20.254 255.255.255.192
R1(config-subif)#no shut
R1(config-subif)#exit
R1(config)#int eth0/1.30
R1(config-subif)#encapsulation dot1q 30
R1(config-subif)#ip address 10.3.30.254 255.255.255.192
R1(config-subif)#no shut
R1(config-subif)#exit
R1(config)#int eth0/1.40
R1(config-subif)#encapsulation dot1q 40
R1(config-subif)#ip address 10.3.40.254 255.255.255.192
R1(config-subif)#no shut
R1(config-subif)#exit
R1(config)#int eth0/1.50                         
R1(config-subif)#encapsulation dot1q 50                
R1(config-subif)#ip address 10.3.50.254 255.255.255.192
R1(config-subif)#no shut
R1(config-subif)#exit
R1(config)#int eth0/1.60
R1(config-subif)#encapsulation dot1q 60                
R1(config-subif)#ip address 10.3.60.254 255.255.255.192
R1(config-subif)#no shut
R1(config-subif)#exit
```

Configuration VLANS sur le switch1 :

```
IOU1#conf t
Enter configuration commands, one per line.  End with CNTL/Z.
IOU1(config)#vlan 10
IOU1(config-vlan)#name administrators
IOU1(config-vlan)#no shut
IOU1(config-vlan)#exit
IOU1(config)#vlan 20
IOU1(config-vlan)#name users
IOU1(config-vlan)#no shut
IOU1(config-vlan)#exit
IOU1(config)#vlan 30
IOU1(config-vlan)#name stagiaires
IOU1(config-vlan)#no shut
IOU1(config-vlan)#exit
IOU1(config)#vlan 60
IOU1(config-vlan)#name printers
IOU1(config-vlan)#no shut
IOU1(config-vlan)#exit
```

* A faire pour les administrateur :

```
IOU1(config)#int eth0/2
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 10
IOU1(config-if)#no shut
IOU1(config-if)#exit
```

* A faire pour les utilisateurs :
```
IOU1(config)#int eth1/0
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 20
IOU1(config-if)#no shut
IOU1(config-if)#exit
```

* A faire pour les stagiaires
```
IOU1(config)#int eth2/1
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 30 
IOU1(config-if)#no shut
IOU1(config-if)#exit
```

* A faire pour les imprimantes
```
IOU1(config)#int eth0/3
IOU1(config-if)#switchport mode access
IOU1(config-if)#switchport access vlan 60
IOU1(config-if)#no shut
IOU1(config-if)#exit
```

* Sur le Switch 1, exécutez les commandes suivantes :

```
IOU1#conf t
IOU1(config)#int eth0/1 
IOU1(config-if)#switchport trunk encapsulation dot1q
IOU1(config-if)#switchport mode trunk
IOU1(config-if)#switchport trunk allowed vlan 40,50,10,20,60
IOU1(config-if)#no shut
IOU1(config-if)#exit
IOU1(config)#exit
```

* Sur le Switch 2, entrez les commandes suivantes :
```
IOU2#conf t
IOU2(config)#int eth0/0 
IOU2(config-if)#switchport trunk encapsulation dot1q
IOU2(config-if)#switchport mode trunk
IOU2(config-if)#switchport trunk allowed vlan 40,50,10,20,60
IOU2(config-if)#no shut
IOU2(config-if)#exit
IOU2(config)#vlan 40
IOU2(config-vlan)#name serveurs
IOU2(config-vlan)#no shut
IOU2(config-vlan)#exit
IOU2(config)#vlan 50
IOU2(config-vlan)#name sensible-serveurs
IOU2(config-vlan)#no shut
IOU2(config-vlan)#exit
```


* Nombre de routeur et de switches :

    * 1 routeur relié au NAT
    * 2 switchs (dont 1 relié à toutes les machines sauf la salle serveurs)


Machines	| VLAN | net1 		  | net2 | net3 | net4 | net5 | net6 |
------- | ---- | ------------- | ---- | ---- | ---- | -----|------|
ADMINS  | 10   | `10.3.10.1` |   x  |   x  |   x  |  x   |   x  |
USERS   | 20   |       x       |   `10.3.20.1`  |   x  |   x  |  x   |   x  |
STAGIAIRES| 30 |       x       |   x  |   `10.3.30.1`  |   x  |  x   |   x  |
SERVEURS| 40   |       x       |   x  |   x  |   `10.3.40.1`  |  x   |   x  |
SS      | 50   |       x       |   x  |   x  |   x  |  `10.3.50.1`   |   x  |
IMPRIMANTES| 60|       x       |   x  |   x  |   x  |  x   |   `10.3.60.1`  |
R1      |   x  |  `10.3.10.254` | `10.3.20.254` | `10.3.30.254` | `10.3.40.254` | `10.3.50.254` | `10.3.60.254` |
 

* Sur le Switch 1, exécuter les commandes suivantes :

```
IOU1#conf t
IOU1(config)#int eth0/2
IOU1(config-if)#switchport trunk encapsulation dot1q
IOU1(config-if)#switchport mode trunk
IOU1(config-if)#switchport trunk allowed vlan 10,40,50,60
IOU1(config-if)#no shut  
IOU1(config-if)#exit
IOU1(config)#int eth1/0
IOU1(config-if)#switchport trunk encapsulation dot1q
IOU1(config-if)#switchport mode trunk
IOU1(config-if)#switchport trunk allowed vlan 20,40,60
IOU1(config-if)#no shut  
IOU1(config-if)#exit
IOU1(config)#int eth2/1
IOU1(config-if)#switchport trunk encapsulation dot1q
IOU1(config-if)#switchport mode trunk
IOU1(config-if)#switchport trunk allowed vlan 30,60
IOU1(config-if)#no shut  
IOU1(config-if)#exit
IOU1(config)#int eth2/0
IOU1(config-if)#switchport trunk encapsulation dot1q
IOU1(config-if)#switchport mode trunk
IOU1(config-if)#switchport trunk allowed vlan 10,20,30,40,60
IOU1(config-if)#no shut  
IOU1(config-if)#exit
IOU1(config)#exit
```

* Sur le Switch 2, entrez les commandes suivantes :

```
IOU2#conf t
IOU2(config)#int eth0/3
IOU2(config-if)#switchport trunk encapsulation dot1q
IOU2(config-if)#switchport mode trunk
IOU2(config-if)#switchport trunk allowed vlan 10,20,40,60
IOU2(config-if)#no shut  
IOU2(config-if)#exit
IOU2(config)#int eth0/1
IOU2(config-if)#switchport trunk encapsulation dot1q
IOU2(config-if)#switchport mode trunk
IOU2(config-if)#switchport trunk allowed vlan 10,50
IOU2(config-if)#no shut  
IOU2(config-if)#exit
IOU2(config)#exit
```