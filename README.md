# Simulation d'une Infrastructure Réseau Multi Sites Cisco Packet Tracer
### Projet de simulation d'un réseau d'entreprise multi-sites interconnectant trois LANs (Casablanca, Rabat, Agadir) avec un LAN Central, implémentant des protocoles avancés de routage, sécurité, VoIP, et services réseau.

# Configuration d'Infrastructure WAN (Cisco)

Ce dépôt contient la configuration détaillée d'une infrastructure réseau locale centralisée. Le projet met en œuvre des solutions avancées de commutation et de sécurité pour garantir la performance et la protection des données au sein d'une entreprise.

## 🛠️ Détails Techniques de la Configuration

### 1. Segmentation et Gestion du Trafic
* **VLANs Déployés :** Organisation rigoureuse du trafic (VLAN Admin, RH, DEV, IT, Voix IP).
* **VLAN Natif :** Utilisation du VLAN 90 pour la sécurité des liens Trunk.
* **VLAN Voix :** Optimisation du trafic pour la téléphonie IP sur les ports d'accès.

### 2. Sécurité
* #### **Port-Security :** * Limitation du nombre d'adresses MAC par port (Maximum 3).
    * Utilisation de l'apprentissage dynamique avec `Sticky MAC`.
    * Mode de violation `Restrict` pour bloquer les accès non autorisés sans arrêter le port.
* #### **Spanning-Tree (PVST+) :** * Activation de `PortFast` pour une connectivité immédiate des terminaux.
    * Activation de `BPDU Guard` pour protéger le réseau contre les boucles accidentelles sur les ports d'accès.
* #### **ACLs :** 
 *  Accès SSH Restreint (Management) :  L'accès aux équipements via SSH est strictement limité aux adresses IP autorisées.
    *  Admin Central : L'administrateur situé dans le segment central (IP: 4.0.0.5) dispose d'un accès global à tous les équipements du réseau WAN.
    *  Admin Locaul : Chaque administrateur réseau local (LAN) est autorisé à accéder uniquement aux équipements de son propre site (ex: Rabat/Agadir/Casablanca).
      
*   Contrôle d'Accès Web HTTP
    * Accès Inter-LAN (Local) : Chaque réseau local (LAN) est autorisé à accéder exclusivement au serveur Web qui lui est rattaché, garantissant une segmentation optimale des ressources.
    * Privilèges Admin Central : L'administrateur central (Admin Central) bénéficie d'un accès illimité, lui permettant de consulter et de gérer tous les serveurs Web sur l'ensemble des sites (Rabat,             Agadir, Casablanca).
    * Sécurisation du Flux : Utilisation d'ACL étendues pour autoriser uniquement le trafic sur le ports 80 (HTTP) , tout en bloquant toute autre tentative de connexion non autorisée.
      
* Sécurisation du Service FTP
    * Accès Privilégié (Admins Uniquement) : Seuls les terminaux identifiés comme "Admin" (Admin Central et Admins LAN) sont autorisés à établir une connexion avec le serveur FTP.
 
* Restriction du Protocole ICMP (Ping)
    * Isolation des VLANs : Le trafic "Ping" (Echo Request) est totalement interdit entre les différents VLANs au sein de chaque site local pour empêcher les utilisateurs non autorisés de tester la               connectivité des autres segments.
    * Admin Central : Autorisé à effectuer des tests de connectivité (Ping) vers n'importe quel équipement ou hôte sur l'ensemble du réseau WAN/LAN.
    * Admin Local : Autorisé à utiliser le Ping uniquement pour diagnostiquer les équipements au sein de son propre site (LAN).


### 3. Haute Disponibilité et Services
* **EtherChannel :** Agrégation de liens (LACP/PAgP) entre les switches pour augmenter la bande passante et assurer la redondance.
* **Services Réseau :** Configuration des accès pour serveurs WEB, MAIL, DNS et FTP.
* **Administration Sécurisée :** Création d'utilisateurs avec privilèges et chiffrement des mots de passe.

## 📸 Topologie du Projet

<img width="3429" height="2550" alt="new" src="https://github.com/user-attachments/assets/814eee23-39a7-41b9-b57d-ebd6cd59e2f9" />

### Lan Central

<img width="780" height="621" alt="central2" src="https://github.com/user-attachments/assets/de4e0713-8748-49fc-9871-6342889cb0ea" />

### Lan Rabat
 
<img width="1089" height="1015" alt="rabat" src="https://github.com/user-attachments/assets/04ccc2bb-fb89-4f0f-abab-c4320b3caac0" />

### Lan Agadir 

<img width="1099" height="1148" alt="agadir" src="https://github.com/user-attachments/assets/a32b0eba-332e-41ad-b4d8-f78f85cf13bb" />

### Lan Casablanca

<img width="932" height="1367" alt="casa" src="https://github.com/user-attachments/assets/3551242d-794c-4ef9-b2d3-5df77b74aa12" />





