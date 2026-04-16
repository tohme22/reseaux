---
title: "VPN IPSec"
description: "reseaux"
draft: false
weight: 2
---

### Sommaire 

1. [Technologie VPN](#1-technologie-vpn)
2. [Types de VPN](#2-type-de-vpn)
3. [Technologie IPSec](#3-technologie-ipsec)
4. [Création des tunnels IPSec](#4-création-des-tunnels-ipsec)
5. [Configuration du VPN IPSec](#5-configuration-du-vpn-ipsec)
---

### 1. Technologie VPN

- Les organisations utilisent les **réseaux privés virtuels (VPN)** pour créer des connexions de bout en bout.
- Un VPN est **virtuel** en ce sens qu'il transporte des informations au sein d'un réseau privé, mais que ces informations sont effectivement transférées via un réseau public.
- Un VPN est **privé**, dans le sens où le trafic est chiffré pour assurer **la confidentialité** des données pendant qu'il est transporté à travers le réseau public.

Example: Un **pare-feu Cisco Adaptive Security Appliance (ASA)** aide les entreprises à fournir une connectivité sécurisée, y compris des **VPN** et un accès permanent aux filiales distantes en se connectant avec **un autre serveur VPN** pour créer un VPN de site à site.

![](../images/image27.png?height=500&classes=inline)

---
### 2. Types de VPN

#### 2.1 VPN d'accès distant

Les VPN d'accès à distance permettent aux **utilisateurs distants et mobiles** de se connecter en toute sécurité à l'entreprise. 

Il existe deux types de VPN d'accès à distance:

- **VPN SSL sans client** : La connexion est sécurisée à l'aide d'une connexion **SSL par navigateur Web**.

- **VPN basée sur le client** : Un logiciel client VPN tel que **Cisco AnyConnect, OPenVPN** ou autre doit être installé sur l’ordinateur de l'utilisateur distant. Les utilisateurs doivent initier la connexion VPN à l’aide du client VPN, puis s’authentifier auprès de la passerelle VPN de destination.

Le logiciel client VPN chiffre le trafic à l’aide d’**IPsec ou de SSL** et le transfère via Internet vers la passerelle VPN de destination.


![](../images/image28.png?height=450&classes=border,shadow,inline)

#### 2.2 VPN Site à site

- Les VPN de site à site connectent des réseaux sur un réseau non fiable tel qu'Internet.
- Les hôtes finaux envoient et reçoivent du trafic TCP / IP non chiffré normal via **une passerelle VPN**.
- La passerelle VPN **encapsule et crypte le trafic** sortant d'un site et envoie le trafic via le **tunnel VPN** à la passerelle VPN sur le site cible. 
- La réception de la passerelle VPN **élimine les en-têtes, déchiffre le contenu** et relaie le paquet vers l'hôte cible au sein de son réseau privé.

Les VPN de site à site sont généralement créés et sécurisés en utilisant **la sécurité IP (IPSec)**.

![](../images/image29.png?height=400&classes=border,shadow,inline)

---
### 3. Technologie IPSec

- IPsec est un standard IETF qui définit comment un VPN peut être sécurisé sur des réseaux IP. Il fonctionne au niveau de la **couche 3 (réseau).**
- IPsec **protège et authentifie** les paquets IP entre la source et la destination.
- Il est composé d’un groupe de protocoles qui fourniment:
  * **Authentification (AH)** - Utilise le protocole **IKE (Internet Key Exchange)** pour authentifier la source et la destination. 
  * **Échange de clés** - Utilisé le protocole **Diffie-Helman** pour sécuriser l'échange de clés.
  * **Intégrité** - Utilise des algorithmes de **hachage** pour garantir que les paquets n'ont pas été modifiés entre la source et la destination.
  * **Confidentialité** - Utilise des algorithmes de **chiffrement** pour empêcher les cybercriminels de lire le contenu des paquets.
  
#### 3.1 Authentification

- **Clé pré-partagée (PSK)** - La valeur (PSK) est **entrée manuellement** dans chaque homologue.
- **Rivest, Shamir et Adleman (RSA)** - L'authentification utilise des **certificats numériques** pour authentifier les homologues.

![](../images/image30.png?height=170&classes=border,shadow,inline)

#### 3.2 Échange de clés

- L'algorithme **Diffie-Hellman** permet à deux pairs IPSec d'établir **une clé secrète partagée** sur un canal non sécurisé.
- Il existe plusieurs **groupes Diffie-Hellman (DH)** qui se distinguent selon la longueur des clés utilisées et le type de cryptographie **`DH 1 au DH 24`**.

![](../images/image31.png?height=180&classes=border,shadow,inline)

#### 3.3 Intégrité

- Le hachage (HMAC) est un mécanisme qui assure que les données n’ont pas été altérées pendant leur transmission en générant une **valeur de hachage unique**. 
- Parmi les algorithmes utilisés, **Message-Digest 5 (MD5**) repose sur une clé secrète partagée de **`128 bits`**, tandis que l’algorithme de **hachage sécurisé (SHA)** utilise une clé secrète de **`160 bits`**.

![](../images/image32.png?height=400&classes=border,shadow,inline)

#### 3.4 Confidentialité

Les algorithmes de **cryptage** mis en évidence dans la figure sont tous à clé symétrique:
 * **DES** utilise une clé de **`56 bits`**.
 * **3DES** utilise trois clés de chiffrement **`56 bits`**.
 * **AES** propose trois longueurs de clé différentes: **`128 bits, 192 bits et 256 bits`**.
 * **SEAL** crypte les données en continuant plutôt que de crypter des blocs de données. SEAL utilise une clé de **`160 bits`**.

![](../images/image33.png?height=500&classes=border,shadow,inline)

---
### 4. Création des tunnels IPSec

IPsec utilise le protocole **IKE (Internet Key Exchang)** pour négocier et établir des tunnels sécurisés de réseau privé virtuel (VPN).

> Note: **Cisco** appelle également le protocole IKE protocole **ISAKMP (Internet Security Association and Key Management Protocol)**.

#### IKE a deux phases principales :

#### 4.1 IKE Phase 1 - IKE Security Association (IKE SA ou ISAKMP)

- Authentification - En utilisant une **Clé pré-partagée** ou **Certificat**
- L'algorithme **Diffie-Hellman (DH)** est utilisé pour échanger le matériel de clé pour que les deux parties générent indépendamment **la même clé symétrique partagée**, sans avoir à la transmettre.

![](../images/image34.png?height=500&classes=border,shadow,inline)

#### 4.2 IKE Phase 2 - IPsec Security Association (IPsec SA)

- Utilise les clés créées à la phase 1.
- Les pairs **négocient les paramètres IPsec** : l’algorithme de chiffrement (par exemple, AES), l’algorithme d’authentification (par exemple, SHA) et les politiques de sécurité.

> Remarque: Le tunnel 2 est utilisé quand il y a un échange de données, puis il est détruit, quand il n’y a pas de trafic mais le tunnel de phase 1 reste. La création du tunnel 2 est plus rapide et demande moins de travail. 

![](../images/image35.png?height=500&classes=border,shadow,inline)

---
### 5. Configuration du VPN IPSec

#### Example - Configuration d'un VPN IPSec de site à site entre les routers R1 et R3

![](../images/image36.png?height=320&classes=border,shadow,inline)

#### **Étape 1 - Configuration IKE SA Phase 1 (ISAKMP) - Paramètres de stratégie**

![](../images/image37.png?height=320&classes=border,shadow,inline)

#### 1.1 Activer le paquet technologie de sécurité (securityk9) :

![](../images/image38.png?height=380&classes=inline)

####  1.2 Identifiez le trafic intéressant :

- Configurez **ACL 110** pour identifier le trafic du LAN sur **`R1`** vers le LAN sur **`R3`** comme intéressant.

![](../images/image39a.png?height=30&classes=,inline)

- Configurez les paramètres réciproques sur **`R3`**. 

![](../images/image39b.png?height=31&classes=,inline)

####  1.3 Configurez la stratégie IKE Phase 1 ISAKMP :

- Configurer les propriétés de la stratégie de crypto **ISAKMP** sur **`R1`** avec une clé crypto partagée **(PSK)**. 
- Reportez-vous au **tableau ISAKMP Phase 1** pour connaître les paramètres spécifiques à configurer. 

![](../images/image40.png?height=180&classes=inline)

- Configurez les paramètres réciproques sur **`R3`** avec l’adresse de son interface **`s0/0/1 10.1.1.2`**.

- Affichez les propriétés de la nouvelle stratégie ISAKMP à l’aide de la commande **`show crypto isakmp policy`**

![](../images/image41.png?height=420&classes=border,shadow,inline)

---

#### **Étape 2 - Configuration IKE SA Phase 2 (IPSec SA) - Paramètres de stratégie**

![](../images/image42.png?height=340&classes=border,shadow,inline)

- Créez un **jeu de transformations**, example **`VPN-SET`** pour utiliser les algorithmes **`esp-aes`** et **`esp-sha-hmac`**.

![](../images/image43.png?height=26&classes=inline)

- Créez une **carte cryptographique (crypto map)**, example **`VPN-MAP`** qui lie tous les paramètres de la phase 2 ensemble.
- Utilisez le numéro de séquence **`10`**, identifiez-la comme une carte **`ipsec-isakmp`**. Liez cette carte avec l’**`ACL 110`**, créé à l’étape 2.

![](../images/image44.png?height=180&classes=inline)

- Configurez les paramètres réciproques sur **`R3`** avec l’adresse de son interface **`s0/0/1 10.1.1.2`**

![](../images/image45.png?height=220&classes=,inline)

- Afficher les propriétés de la nouvelle **carte cryptographique (crypto map)** à l’aide de la commande **`show crypto map`**

![](../images/image46.png?height=280&classes=inline)

---

#### **Étape 3 - Liaison la carte cryptographique sur l'interface**

- Liez la carte cryptographique **`VPN-MAP`** à l'interface **`s0/0/0`** sortante du **`R1`**:

![](../images/image47.png?height=350&classes=inline)

- Liez la carte cryptographique **`VPN-MAP`** à l'interface **`s0/0/1`** sortante du **`R3`**:

![](../images/image48.png?height=350&classes=inline)

---

#### **Étape 4 - Vérification de la configuration IPSec VPN**

- Pour vérifier le tunnel **avant tout trafic intéressant**. Émettez la commande **`show crypto ipsec sa`** sur **`R1`**. 
- Notez que le nombre de paquets encapsulés, chiffrés, décapsulés et déchiffrés sont **tous définis sur 0**.

![](../images/image49.png?height=350&classes=,inline)

- Créez du **trafic intéressant** en envoyant une requête **`ping`** du **`PC-A`** au **`PC-C`**.
- Sur **`R1`**, réexécutez la commande **`show crypto ipsec sa`**. Notez que le nombre de paquets est **supérieur à 0**, ce qui indique que le tunnel IPsec VPN fonctionne.

![](../images/image50.png?height=350&classes=inline)





