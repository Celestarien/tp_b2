# TP2 : Network low-level, Switching

## I. Simplest setup

### Topologie mise en place :

```
+-----+        +-------+        +-----+
| PC1 +--------+  SW1  +--------+ PC2 |
+-----+        +-------+        +-----+
```

### Plan d'adressage :

* PC 1 = 10.2.1.1/24
* PC 2 = 10.2.1.2/24

* Verifions la communication des deux machines :
    * Utilisez la commande : ``ping 10.2.1.2`` sur le PC 1
    * Utilisez la commande : ``ping 10.2.1.1`` sur le PC 2


## II. More switches

### Topologie mise en place :

```
                        +-----+
                        | PC2 |
                        +--+--+
                           |
                           |
                       +---+---+
                   +---+  SW2  +----+
                   |   +-------+    |
                   |                |
                   |                |
+-----+        +---+---+        +---+---+        +-----+
| PC1 +--------+  SW1  +--------+  SW3  +--------+ PC3 |
+-----+        +-------+        +-------+        +-----+

```

### Plan d'adressage :

* PC 1 = 10.2.1.1/24
* PC 2 = 10.2.1.2/24
* PC 3 = 10.2.1.3/24

* Verifions la communication des deux machines :
    * Utilisez la commande : ``ping 10.2.1.2`` & ``ping 10.2.1.3``  sur le PC 1
    * Utilisez la commande : ``ping 10.2.1.1`` & ``ping 10.2.1.3`` sur le PC 2
    * Utilisez la commande : ``ping 10.2.1.1`` & ``ping 10.2.1.2`` sur le PC 3

### Table MAC :

* Utilisez la commande : `show mac address-table`
* Les lignes coreespondent au Vlan, Mac Address, Type et Ports

### STP :

* Utiliser la commande : `show spanning-tree` pour voir les informations STP
* Utiliser la commande : `show spanning-tree bridge` pour voir le root bridge
* Schéma explicatif de STP :

![](https://i0.wp.com/fingerinthenet.com/wp-content/uploads/2016/11/stpnew001.png?resize=712%2C378&ssl=1)

*  Confirmez les informations STP :
    * Réaliser `ping 10.2.1.3` à partir du PC 1

* Le lien désactiver par STP est celui qui risque de poser problème, c'est-à-dure de créer une boucle

* Pour changer la priorité d'un switch il faut effectuer la commande : `conf t`

## III. Isolation

### 1. Simple

### Topologie mise en place :

```
+-----+        +-------+        +-----+
| PC1 +--------+  SW1  +--------+ PC3 |
+-----+      10+-------+20      +-----+
                 20|
                   |
                +--+--+
                | PC2 |
                +-----+

```

### Plan d'adressage :

* PC 1 = 10.2.1.1/24
* PC 2 = 10.2.1.2/24
* PC 3 = 10.2.1.3/24

* Verifions la communication des deux machines :
    * Utilisez la commande : ``ping 10.2.1.2`` & ``ping 10.2.1.3``  sur le PC 1
    * Utilisez la commande : ``ping 10.2.1.1`` & ``ping 10.2.1.3`` sur le PC 2
    * Utilisez la commande : ``ping 10.2.1.1`` & ``ping 10.2.1.2`` sur le PC 3

Si tout est bon, PC 2 et PC 3 ne peuvent pas joindre PC 1 et inversement

### 2. Avec trunk

### Topologie mise en place :

```
+-----+        +-------+        +-------+        +-----+
| PC1 +--------+  SW1  +--------+  SW2  +--------+ PC4 |
+-----+      10+-------+        +-------+20      +-----+
                 20|              10|
                   |                |
                +--+--+          +--+--+
                | PC2 |          | PC3 |
                +-----+          +-----+
```

### Plan d'adressage :

* PC 1 = 10.2.1.1/24 | IP `net1` | VLAN : 10
* PC 2 = 10.2.1.1/24 | IP `net2` | VLAN : 20
* PC 3 = 10.2.1.2/24 | IP `net1` | VLAN : 10
* PC 4 = 10.2.1.2/24 | IP `net2` | VLAN : 20

* Verifions la communication des deux machines :
    * Sur PC 1 si l'on ping toutes les machines ont peut voir que nous ne pouvons communiquer qu'avec PC 3
    * Sur PC 4 si on réalise la même procédure nous pouvons voir qu'il communique uniquement avec PC 2

* VLAN + Wireshark :

Un réseau local virtuel ponté est utilisé pour regrouper logiquement des périphériques réseau partageant le même réseau physique.

De cette façon, le trafic réseau d'un groupe de VLAN n'est visible que par les périphériques réseau membres de ce groupe.

Un VLAN spécifique (groupe) se distingue par un ID de VLAN 12 bits unique.

Un administrateur réseau devra regrouper logiquement les périphériques réseau et fournir un ID VLAN unique pour chacun de ces groupes. Ensuite, il connectera chaque port de chaque commutateur (Ethernet) participant à un ou plusieurs de ces identifiants. Après cela, le commutateur transmettra les paquets étiquetés VLAN entrants uniquement aux périphériques réseau situés dans le VLAN spécifique.

## IV. Need perfs

### Topologie mise en place :

```
+-----+        +-------+--------+-------+        +-----+
| PC1 +--------+  SW1  |        |  SW2  +--------+ PC4 |
+-----+      10+-------+--------+-------+20      +-----+
                 20|              10|
                   |                |
                +--+--+          +--+--+
                | PC2 |          | PC3 |
                +-----+          +-----+

```

### Plan d'adressage :

* PC 1 = 10.2.1.1/24 | IP `net1` | VLAN : 10
* PC 2 = 10.2.1.1/24 | IP `net2` | VLAN : 20
* PC 3 = 10.2.1.2/24 | IP `net1` | VLAN : 10
* PC 4 = 10.2.1.2/24 | IP `net2` | VLAN : 20

    * Étant donné que le plan d'adressage est indentique au précédent, PC 1 peut communiquer uniquement avec PC 3 et PC 4 avec PC 2.

### Configurer LACP 

* Utilisez les commandes suivantes : 
    * Sur le Switch 1 :
        * ```interface range gigabitEthernet 0/1 - 2```
        * ```channel-group 1 mode active```
        * ```channel-protocol lacp```
        * ```exit```
        * ```interface port-channel 1```
        * ```switchport mode trunk```
        * ```switchport trunk allowed vlan [Numéros]```
        * ```no shutdown```
        * ```exit```
    * Réaliser la même opération sur le Switch 2

    * Utilisez la commande : ```show interface po1```
        * On peut voir que la bande passante a été doublé