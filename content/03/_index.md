---
title: "Téléphonie VoIP"
description: "reseaux"
draft: false
weight: 3
---

### Sommaire 

1. [Qu’est-ce que la téléphonie IP ?](#1-Quest-ce-que-la-téléphonie-IP-?)
2. [Les Téléphones IP](#2-les-téléphones-ip)
3. [Un VLAN pour la voix](#3-un-vlan-pour-la-voix)
4. [Configuration de base - Cisco VoIP](#4-Configuration-de-base---Cisco-VoIP)
---

### 1. Qu’est-ce que la téléphonie IP ?

- La téléphonie VoIP est une **technologie de communication audio et vidéo qui permet de transmettre la voix et les images via Internet**. 
- La téléphonie VoIP rassemble tous les équipements de l’entreprise **(téléphones fixes, visioconférence, fax, ordinateurs portables)** sur un même réseau.

#### Principaux avantages de la téléphonie VoIP

- **Réduction des frais** : La VoIP est beaucoup moins coûteuse que la téléphonie traditionnelle, sans besoin d’équipements supplémentaires ni de frais de maintenance.  
- **Frais interurbains réduits** : Prmet des tarifs interurbains avantageux et des appels gratuits entre abonnés, même à l’international.
- **Qualité sonore HD** : Les appels voix et vidéo bénéficient d’une haute qualité grâce à une bonne connexion Internet et une configuration adéquate.  
- **Fonctionnalités avancées** : Conférences audio/vidéo, clavardage, partage d’écran, et télécopieur virtuel sont inclus dans le service.  
- **Installation rapide** : Le système s’installe facilement et rapidement avec l’aide de techniciens.  
- **Numéro international** : La VoIP permet d’obtenir un numéro international pour développer sa présence dans d’autres pays.  
- **Boîte vocale améliorée** : Elle offre une messagerie évoluée avec accueil automatisé, enregistrement et statistiques d’appels, notifications par courriel, transcription des messages, écoute à distance, blocage des appels indésirables et gestion simple via une zone client.  

#### Matériel nécessaire à l’implantation de la téléphonie VoIP

![](../images/image51.png?height=600&classes=inline)

- **IPBX (IP Private Branch Exchange)** : terminal reliant les appels, gérable via Internet, Intranet ou logiciel.  
- **Serveur de gestion de communication** : gère les autorisations d’appels entre les terminaux connectés à Internet ou aux logiciels.  
- **Routeur** : transmet la voix via Internet ou cellulaire de façon sécurisée.  
- **Switch** : relie les ports Ethernet.  
- **Téléphone IP** : se branche directement à une prise Ethernet.  
- **Softphone** : logiciel permettant de passer des appels par Internet via un ordinateur plutôt qu’un téléphone.  

---

### 2. Les Téléphones IP

- Les **Téléphones IP** utilisent des technologies **VoIP (Voix sur IP)** pour activer les appels téléphoniques au-dessus d’un réseau IP, tel qu’Internet.  

![](../images/image53.png?height=280&classes=inline)

- Ils sont connectés à un **commutateur** comme n’importe quel autre hôte.  
- Les Téléphones IP possèdent un **commutateur interne à 3 ports** :  
  - 1 port connecté au commutateur externe,  
  - 1 port relié au PC,  
  - 1 port connecté intérieurement au téléphone lui-même.  
  
![](../images/image54.png?height=220&classes=inline)

➡️ Cette configuration permet au **PC et au téléphone IP de partager un seul port** du commutateur. Le trafic du PC transite à travers le téléphone IP avant d’atteindre le commutateur.  

---

### 3. Un VLAN pour la voix

Un **VLAN Voix** est un réseau local virtuel spécialement dédié aux flux de données vocales. Il permet de séparer le trafic voix du trafic data, garantissant **performance** et **sécurité**.  

![](../images/image52.png?height=600&classes=inline)

##### **Avantages principaux**

- **Séparation du trafic** : les paquets voix _tagués_ sont isolés des paquets data _non tagués_.  
- **Qualité de service (QoS)** : meilleure gestion de la bande passante pour éviter coupures et latence.  
- **Sécurité accrue** : les communications voix ne transitent pas sur le même flux que les données usuelles.  
- **Gestion centralisée** : via le routeur, le switch et l’IPBX, les appels sont acheminés efficacement.  

#### Séparation du trafic Voix et Données

- Il est recommandé de **séparer le trafic de « Voix »** (téléphone IP) et le **trafic de « Données »** (PC) en les plaçant dans des **VLAN distincts**.  
- Ceci peut être accompli en utilisant un **VLAN Voix**.  
  - Le trafic du **PC** sera **non-tagué**.  
  - Le trafic du **téléphone IP** sera **tagué** avec un **ID de VLAN**.  

![](../images/image55.png?height=300&classes=inline)

---

### 4. Configuration de base - Cisco VoIP

Cette configuration met en place une infrastructure VoIP simple avec :  
- Un **Routeur Cisco** jouant le rôle de **CME (Communications Manager Express)** et **serveur DHCP**.  
- Un **Commutateur Cisco** configuré avec des VLAN séparés pour **Voix** et **Données**.  
- Des **téléphones IP** enregistrés sur le CME avec des numéros attribués.  

![](../images/Topology-IP-Phone.png?height=400&classes=inline)

#### **1. Configuration du Routeur**

```yaml
conf t
hostname R1

! DHCP VLAN 20
ip dhcp pool VLAN20
 network 10.1.20.0 255.255.255.0
 default-router 10.1.20.1
 option 150 ip 10.1.10.2
exit

! DHCP VLAN 30
ip dhcp pool VLAN30
 network 10.1.30.0 255.255.255.0
 default-router 10.1.30.1
 option 150 ip 10.1.10.2
exit

! Connexion au CME
interface GigabitEthernet0/0
 ip address 10.1.10.1 255.255.255.0
 no shutdown
exit

! Connexion au Switch
interface GigabitEthernet0/1
 no ip address
 no shutdown
exit

! VLAN 20
interface GigabitEthernet0/1.20
 encapsulation dot1Q 20
 ip address 10.1.20.1 255.255.255.0
exit

! VLAN 30
interface GigabitEthernet0/1.30
 encapsulation dot1Q 30
 ip address 10.1.30.1 255.255.255.0
exit

end
wr

```

#### **2. Configuration de CME (Communications Manager Express)**

```yaml
conf t
hostname CME

! Connexion au R1
interface GigabitEthernet0/0
 no shutdown
 ip address 10.1.10.2 255.255.255.0
exit

ip route 0.0.0.0 0.0.0.0 10.1.10.1

! ==========================================
! Configuration de Cisco CallManager Express
! ==========================================
!
telephony-service
 !
 ! Nombre maximum de téléphones IP
 max-ephones 2
 !
 ! Nombre maximum de numéros (Directory Numbers)
 max-dn 2
 !
 ! Adresse IP du CME et port TCP 2000
 ! utilisé pour l'enregistrement SCCP des téléphones
 ip source-address 10.1.10.2 port 2000
 !
 ! Attribution automatique des numéros
 ! aux téléphones lors de leur enregistrement
 auto assign 1 to 2
exit

!
! ==========================================
! Configuration des numéros de téléphone
! ==========================================
!
! Premier numéro : 54001
!
ephone-dn 1
 number 54001
exit

!
! Deuxième numéro : 54002
!
ephone-dn 2
 number 54002
exit

end
wr

```

#### **3. Configuration du Commutateur**

```yaml
conf t
hostname S1

vlan 20
 name VLAN20
exit

vlan 30
 name VLAN30
exit

! Trunk au R1
interface GigabitEthernet0/0
 switchport trunk encapsulation dot1q
 switchport mode trunk
 no shutdown
exit

! IP Phone 1
interface GigabitEthernet0/1
 switchport mode access
 switchport access vlan 20
 no shutdown
exit

! IP Phone 2
interface GigabitEthernet0/2
 switchport mode access
 switchport access vlan 30
 no shutdown
exit

end
wr

```

#### **3. Vérification**

- Démarez les **deux téléphones** :

![](../images/Phone1.png?height=600&classes=inline)

![](../images/Phone2.png?height=620&classes=inline)

- Vérifiez que le **router (CME)** a registré les deux téléphones

![](../images/ephone.png?height=300&classes=border,inline)

- Essayez **d’appeler d’un téléphone à l’autre**

![](../images/image60.png?height=800&classes=inline)

