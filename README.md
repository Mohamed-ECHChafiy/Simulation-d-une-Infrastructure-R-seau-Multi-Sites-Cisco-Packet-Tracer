# 🌐 Simulation d'une Infrastructure Réseau Multi-Sites — Cisco Packet Tracer

> Projet de simulation d'un réseau d'entreprise multi-sites interconnectant trois LANs (Casablanca, Rabat, Agadir) avec un LAN Central, implémentant des protocoles avancés de routage, sécurité, VoIP, et services réseau.

---

## 🔍 Aperçu du Projet

Ce projet simule une infrastructure réseau d'entreprise distribuée sur **4 sites géographiques** :

| Site | Rôle | Routeur | Nb Switches |
|------|------|---------|-------------|
| **Central** | Hub principal / WAN | R-CENTRAL | SW-CENTRAL |
| **Casablanca** | Siège régional | R-CASA | MLS-CASA + SW1/SW2-CASA |
| **Rabat** | Agence Rabat | R-RABAT | SW1/SW2/SW3-RABAT |
| **Agadir** | Agence Agadir | R-AGADIR | SW1/SW3-AGADIR |

---

## 🗺️ Topologie Réseau

Les sites sont interconnectés via des **liaisons série WAN (PPP/CHAP)** :

<img width="500" height="2550" alt="trtr" src="https://github.com/user-attachments/assets/801dd8b8-9ea7-4c15-ab9f-58318de338fd" />


## 🏗️ Architecture par Site

### 🏢 LAN Central
- **Routeur** : R-CENTRAL  
- **Switch** : SW-CENTRAL  
- **Réseau ADMIN** : `4.0.0.0/8`  
- **Réseau Voice** : `172.168.0.0/16`  
- Services hébergés : WEB (Casablanca, Rabat, Agadir), DNS, FTP, NTP/Syslog/Email  
- Connexion WAN via Serial (PPP/CHAP) vers les trois sites  

### 🏙️ LAN Casablanca
- **Routeur** : R-CASA | **MLS** : MLS-CASA  
- **Switches** : SW1-CASA, SW2-CASA  
- VLANs : ADMIN (90), IT (2), DEV (4), RH (6) + VLANs Voice  
- Réseau ADMIN : `192.168.90.0/24`  
- Routage : EIGRP 100  
- Accès Internet : Serial `212.12.13.2/30`
- DHCP est activé sur le Multi layer switch

### 🏙️ LAN Rabat
- **Routeur** : R-RABAT  
- **Switches** : SW1-RABAT (VTP Server), SW2-RABAT (VTP Client), SW3-RABAT (VTP Client)  
- VLANs : ADMIN (90), IT (2), DEV (4), RH (6) + VLANs Voice  
- Réseau ADMIN : `192.168.1.0/24`  
- VTP Domain : `rabat.ma` | Routage : EIGRP 100    
- DHCP est activé sur le serveur

### 🏙️ LAN Agadir
- **Routeur** : R-AGADIR  
- **Switches** : SW1-AGADIR, SW3-AGADIR  
- VLANs : ADMIN (90), IT (2), DEV (4), RH (6) + VLANs Voice  
- Réseau ADMIN : `192.168.5.0/24`  
- DHCP est activé sur le routeur    

---

## ⚙️ Technologies & Protocoles

### Routage
| Protocole | Utilisé sur |
|-----------|-------------|
| **EIGRP 100** | Rabat ↔ Casablanca ↔ Agadir |
| **OSPF Area 0** | LAN Central (redistribution RIP) |
| **RIP** | Redistribué vers OSPF au Central |

### Switching
| Fonctionnalité | Détail |
|----------------|--------|
| **VLANs** | ADMIN (90/91), IT (2/3), DEV (4/5), RH (6/7) |
| **Trunking** | 802.1Q, native VLAN 90 |
| **EtherChannel** | LACP (active/passive) et PAgP (desirable/auto) |
| **STP** | Rapid-PVST+ avec priorités par VLAN |
| **VTP** | Domaine par site (Server/Client) |
| **Router-on-a-Stick** | Sous-interfaces dot1Q sur routeurs |

### Services Réseau
| Service | Localisation |
|---------|-------------|
| **DHCP** | Central (Voice), Rabat (relay), Agadir (local), Casablanca MLS |
| **DNS** | Serveur central `4.0.0.254` |
| **NTP** | Serveur par site (`x.x.x.253`) |
| **Syslog** | Collecteur par site (`x.x.x.254`) |
| **FTP** | Serveur central `4.0.0.253` |
| **TFTP** | Par site |
| **Web/HTTP** | Serveurs dédiés par site |
| **Email** | Par site |

### Sécurité
| Mécanisme | Détail |
|-----------|--------|
| **SSH v2** | Accès VTY sécurisé sur tous les équipements |
| **PPP/CHAP** | Authentification sur toutes les liaisons WAN |
| **ACL SSH** | Restriction d'accès SSH par IP admin |
| **ACL BLK** | Filtrage HTTP/FTP/ICMP par site |
| **Port-Security** | Sticky MAC + violation restrict sur tous les ports access |
| **BPDUGuard** | Activé sur ports PortFast |
| **NetFlow** | Export v9 vers collecteur par site |

### VoIP
| Paramètre | Valeur |
|-----------|--------|
| **Protocole** | SCCP (Cisco IP Phones 7960) |
| **Numérotation** | Central: 9xx, Rabat: 100, Agadir: 200, Casa: 300 |
| **DHCP Option 150** | TFTP server pour phones |
| **Dial-peers VoIP** | Inter-sites via adresses Serial |

---

## 📊 Plan d'Adressage IP

### Réseaux WAN (Liaisons Série)
| Liaison | Réseau | Interface |
|---------|--------|-----------|
| Central ↔ WAN | `3.2.1.0/30` | Serial R-CENTRAL: `3.2.1.2` / IPS: `3.2.1.1` |
| Rabat ↔ Casa | `10.0.0.0/30` | R-RABAT: `10.0.0.2` / R-CASA: `10.0.0.1` |
| Rabat ↔ Agadir | `10.0.0.4/30` | R-RABAT: `10.0.0.5` / R-AGADIR: `10.0.0.6` |
| Casa ↔ Agadir | `10.0.0.8/30` | R-CASA: `10.0.0.9` / R-AGADIR: `10.0.0.10` |
| Casa ↔ Internet | `212.12.13.0/30` | R-CASA: `212.12.13.2` / IPS `212.12.13.1` |
| Casa ↔ MLS | `20.0.0.0/29` | R-CASA: `20.0.0.1` / MLS: `20.0.0.2` |

### Réseaux LAN par Site

#### LAN Central
| VLAN | Réseau | Gateway |
|------|--------|---------|
| ADMIN (100) | `4.0.0.0/8` | `4.0.0.1` |
| Voice (101) | `172.168.0.0/16` | `172.168.0.1` |

#### LAN Rabat
| VLAN | Réseau | Gateway |
|------|--------|---------|
| ADMIN (90) | `192.168.1.0/24` | `192.168.1.1` |
| IT (2) | `192.168.2.0/24` | `192.168.2.1` |
| DEV (4) | `192.168.3.0/24` | `192.168.3.1` |
| RH (6) | `192.168.4.0/24` | `192.168.4.1` |

#### LAN Agadir
| VLAN | Réseau | Gateway |
|------|--------|---------|
| ADMIN (90) | `192.168.5.0/24` | `192.168.5.1` |
| IT (2) | `192.168.6.0/24` | `192.168.6.1` |
| DEV (4) | `192.168.7.0/24` | `192.168.7.1` |
| RH (6) | `192.168.8.0/24` | `192.168.8.1` |

#### LAN Casablanca
| VLAN | Réseau | Gateway |
|------|--------|---------|
| ADMIN (90) | `192.168.90.0/24` | `192.168.90.1` |
| IT (2) | `192.168.10.0/24` | `192.168.10.1` |
| DEV (4) | `192.168.11.0/24` | `192.168.11.1` |
| RH (6) | `192.168.12.0/24` | `192.168.12.1` |

---

## 🔒 Sécurité

### ACLs

#### SSH
- Admin Central : L'administrateur situé dans le segment central (IP: 4.0.0.5) dispose d'un accès global à tous les équipements du réseau WAN.
- Admin Locaul : Chaque administrateur réseau local (LAN) est autorisé à accéder uniquement aux équipements de son propre site (ex: Rabat/Agadir/Casablanca).

####  Restriction du Protocole ICMP (Ping)
- Isolation des VLANs : Le trafic "Ping" (Echo Request) est totalement interdit entre les différents VLANs au sein de chaque site local pour empêcher les utilisateurs non autorisés de tester la connectivité des         autres segments.
- Admin Central : Autorisé à effectuer des tests de connectivité (Ping) vers n'importe quel équipement ou hôte sur l'ensemble du réseau WAN/LAN.
- Admin Local : Autorisé à utiliser le Ping uniquement pour diagnostiquer les équipements au sein de son propre site (LAN).

#### Politique HTTP par site
- LAN Rabat → Serveur WEB Central (`4.0.0.251`)
  
<img width="500" height="653" alt="rabat10" src="https://github.com/user-attachments/assets/2963215d-ca62-4ece-88d6-3a7c13beab36" />


- LAN Agadir → Serveur WEB Central (`4.0.0.252`)  
- LAN Casa → Serveur WEB Central (`4.0.0.250`)
- Tout autre HTTP → **DENY**

---

## 
---

## 👤 Auteur

**Mohamed ECH-chafiy**  
Étudiant en Infrastructure Digitale — Option Systèmes & Réseaux  
OFPPT, Casablanca,Rahma, Maroc  

---

## 📝 Notes

- Simulé avec **Cisco Packet Tracer**
- FTP credentials : `ADMIN / 12345`
- NTP/Syslog configurés sur chaque site

---

*Projet réalisé dans le cadre de la formation Infrastructure Digitale — OFPPT*
