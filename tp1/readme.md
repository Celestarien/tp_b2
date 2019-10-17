# TP1 : Back to basics

## I. Gather informations

* Pour récupérer une liste des cartes réseau avec leur nom, leur IP et leur adresse MAC :
    * ```ip a```
* Pour déterminer si les cartes réseaux ont récupéré une IP en DHCP :
    * Faire un ``ip a`` et vérifier qu'il y a "dynamic"
* hint :
    * ```ls /var/lib/NetworkManager```
* Pour récupérer des infos sur le bail actuel d'une interface donnée avec :
    * ``sudo nmcli con show <INTERFACE_NAME>`` (pour toutes les infos)
    * ``sudo nmcli -f DHCP4 con show <INTERFACE_NAME>`` (pour les infos DHCP uniquement)
* Afficher la table de routage de la machine avec :
    * ``ip route``
    * Données affichés :
        * Le réseau A : 190.1.1.0/255.255.255.0 (de 190.1.1.1 à 190.1.1.254)
        * L'adresse IP du poste A sur le réseau A
        * Le réseau B : 193.17.1.0/255.255.255.0 (de 193.17.1.1 à 193.17.1.254)
        * L'adresse IP du poste B sur le réseau B
* Afficher la table ARP :
    * ``yum install net-tools -y`` (Sauf si déjà installé // "-y" permet d'accepter directement toutes les conditions)
    * ``arp -a``
* Afficher les ports en écoute :
    * ``ss -ltupn``        😉
* Récupérer la liste des DNS utilisés par la machine : 
    * ``nmcli device show <INTERFACE NAME>``
* Afficher l'état actuel du firewall :
    * ``nftables``
## II. Edit configuration

*  Modifier la configuration de la carte réseau privée :
    * ``vim /etc/sysconfig/network-scripts/ifcfg-eth0``
*  Dans la VM définir une IP statique pour cette nouvelle carte :
    * ``BOOTPROTO=static``
*  Modifier la configuration du système pour que le serveur SSH tourne sur le port 2222 :
    * ``vi /etc/ssh/sshd_config``
* Analyser les trames de connexion au serveur SSH
    * ``ss -lntp | grep ssh``

## IV. Autres applications et métrologie

1) Commandes

* Expliquer ``iftop`` :  
 Sert à jeter un œil sur ce qui transite par votre réseau (entrant/sortant) et sur la quantité de bande passante qu’utilisent les applications ou services connectés.  

 2) Cockpit

* C'est quoi ?  
Cockpit  est un gestionnaire de serveur Web.

* Trouver sur quel port écoute Cockpit :
    * ``ss -lntp | grep cockpit``

