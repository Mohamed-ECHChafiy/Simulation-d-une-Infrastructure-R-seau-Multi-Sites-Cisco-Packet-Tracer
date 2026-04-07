# Simulation d'une Infrastructure Réseau Multi Sites Cisco Packet Tracer
### Projet de simulation d'un réseau d'entreprise multi-sites interconnectant trois LANs (Casablanca, Rabat, Agadir) avec un LAN Central, implémentant des protocoles avancés de routage, sécurité, VoIP, et services réseau.

# Configuration d'Infrastructure LAN Centralisée (Cisco)

Ce dépôt contient la configuration détaillée d'une infrastructure réseau locale centralisée. Le projet met en œuvre des solutions avancées de commutation et de sécurité pour garantir la performance et la protection des données au sein d'une entreprise.

## 🛠️ Détails Techniques de la Configuration

### 1. Segmentation et Gestion du Trafic
* **VLANs Déployés :** Organisation rigoureuse du trafic (VLAN Admin, RH, Serveurs, Voix IP).
* **VLAN Natif :** Utilisation du VLAN 90 pour la sécurité des liens Trunk.
* **VLAN Voix :** Optimisation du trafic pour la téléphonie IP sur les ports d'accès.

### 2. Sécurité de Couche 2 (Layer 2 Security)
* **Port-Security :** * Limitation du nombre d'adresses MAC par port (Maximum 3).
    * Utilisation de l'apprentissage dynamique avec `Sticky MAC`.
    * Mode de violation `Restrict` pour bloquer les accès non autorisés sans arrêter le port.
* **Spanning-Tree (PVST+) :** * Activation de `PortFast` pour une connectivité immédiate des terminaux.
    * Activation de `BPDU Guard` pour protéger le réseau contre les boucles accidentelles sur les ports d'accès.

### 3. Haute Disponibilité et Services
* **EtherChannel :** Agrégation de liens (LACP/PAgP) entre les switches pour augmenter la bande passante et assurer la redondance.
* **Services Réseau :** Configuration des accès pour serveurs WEB (Casablanca), DNS et FTP.
* **Administration Sécurisée :** Création d'utilisateurs avec privilèges et chiffrement des mots de passe.

## 📸 Topologie du Projet

<img width="3429" height="2550" alt="new" src="https://github.com/user-attachments/assets/814eee23-39a7-41b9-b57d-ebd6cd59e2f9" />

### Lan Central





