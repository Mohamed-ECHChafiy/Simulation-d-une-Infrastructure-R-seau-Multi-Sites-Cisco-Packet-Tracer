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

### VoIP
| Paramètre | Valeur |
|-----------|--------|
| **Protocole** | SCCP (Cisco IP Phones 7960) |
| **Numérotation** | Central: 900, Rabat: 100, Agadir: 200, Casa: 300 |
| **DHCP Option 150** | TFTP server pour phones |
| **Dial-peers VoIP** | Les téléphone administratifs sont connectés entre eux réseau wan |

---

## 📊 Plan d'Adressage IP

### Réseaux WAN (Liaisons Série)
| Liaison | Réseau | Interface |
|---------|--------|-----------|
| Central ↔ WAN | `3.2.1.0/30` | Serial R-CENTRAL: `3.2.1.2` / WAN: `3.2.1.1` |
| Rabat ↔ Casa | `10.0.0.0/30` | R-RABAT: `10.0.0.2` / R-CASA: `10.0.0.1` |
| Rabat ↔ Agadir | `10.0.0.4/30` | R-RABAT: `10.0.0.5` / R-AGADIR: `10.0.0.6` |
| Casa ↔ Agadir | `10.0.0.8/30` | R-CASA: `10.0.0.9` / R-AGADIR: `10.0.0.10` |
| Casa ↔ Internet | `212.12.13.0/30` | R-CASA: `212.12.13.2` / WAN `212.12.13.1` |
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
| Admin voice (91) | `172.168.1.0/24` | `172.168.1.1`
| IT (2) | `192.168.2.0/24` | `192.168.2.1` |
| IT voice (3) | `172.168.2.0/24` | `172.168.2.1` |
| DEV (4) | `192.168.3.0/24` | `192.168.3.1` |
| DEV voice (5) | `172.168.3.0/24` | `172.168.3.1` |
| RH (6) | `192.168.4.0/24` | `192.168.4.1` |
| RH voice (7) | `172.168.4.0/24` | `172.168.4.1` |

#### LAN Agadir
| VLAN | Réseau | Gateway |
|------|--------|---------|
| ADMIN (90) | `192.168.5.0/24` | `192.168.5.1` |
| Admin voice (91) | `172.168.5.0/24` | `172.168.5.1` |
| IT (2) | `192.168.6.0/24` | `192.168.6.1` |
| IT voice (3) | `172.168.6.0/24` | `172.168.6.1` |
| DEV (4) | `192.168.7.0/24` | `192.168.7.1` |
| DEV voice (5) | `172.168.7.0/24` | `172.168.7.1` |
| RH (6) | `192.168.8.0/24` | `192.168.8.1` |
| RH voice (7) | `172.168.8.0/24` | `172.168.8.1` |

#### LAN Casablanca
| VLAN | Réseau | Gateway |
|------|--------|---------|
| ADMIN (90) | `192.168.90.0/24` | `192.168.90.1` |
| Admin voice (91) | `172.168.90.0/24` | `172.168.90.1` |
| IT (2) | `192.168.10.0/24` | `192.168.10.1` |
| IT voice (3) | `172.168.10.0/24` | `172.168.10.1` |
| DEV (4) | `192.168.11.0/24` | `192.168.11.1` |
| DEV voice (5) | `172.168.11.0/24` | `172.168.11.1` |
| RH (6) | `192.168.12.0/24` | `192.168.12.1` |
| RH voice (7) | `172.168.12.0/24` | `172.168.12.1` |

---

## 🔒 Sécurité

### ACLs

#### SSH
- Admin Central : L'administrateur situé dans le segment central (IP: 4.0.0.5) dispose d'un accès global à tous les équipements du réseau WAN.
- Admin Locaul : Chaque administrateur réseau local (LAN) est autorisé à accéder uniquement aux équipements de son propre site (ex: Rabat/Agadir/Casablanca).

####  Restriction du Protocole ICMP (Ping)
- Isolation des VLANs : Le trafic "Ping" (Echo Request) est totalement interdit entre les différents VLANs au sein de chaque site local pour empêcher les utilisateurs       non autorisés de tester la connectivité des autres segments.
- Admin Central : Autorisé à effectuer des tests de connectivité (Ping) vers n'importe quel équipement ou hôte sur l'ensemble du réseau WAN/LAN.
- Admin Local : Autorisé à utiliser le Ping uniquement pour diagnostiquer les équipements au sein de son propre site (LAN).

#### Politique HTTP par site
- LAN Rabat → Serveur WEB Central (`4.0.0.251`)
- LAN Agadir → Serveur WEB Central (`4.0.0.250`)
- LAN Casa → Serveur WEB Central (`4.0.0.252`)
- LAN Central → Tous les Serveur WEB Central
- Tout autre HTTP → **DENY**

---

## TEST
### Lan Central

<img width="600" height="609" alt="lan central" src="https://github.com/user-attachments/assets/8548c67f-8a80-4402-a062-cd25ccfdbddf" />

#### PC & IP Phone 
<img width="352" height="285" alt="image" src="https://github.com/user-attachments/assets/cc28d51f-2133-4e66-9b8c-866deb7e798d" />
<img width="500" height="225" alt="image" src="https://github.com/user-attachments/assets/7c270200-d0ab-4d33-9dc5-1ebc8c363dd2" />


#### Server DNS (`4.0.0.0.254`)

<img width="500" height="475" alt="image" src="https://github.com/user-attachments/assets/9951550a-ef6c-4a0e-bde8-1ffcec25650c" />

#### Server FTP wan / TFTP lan (`4.0.0.253`)

- FTP
<img width="600" height="591" alt="image" src="https://github.com/user-attachments/assets/6f46d6d2-821e-4d98-8b37-30c46726d243" />

- TFTP
<img width="581" height="217" alt="image" src="https://github.com/user-attachments/assets/98e6a001-c8fb-43da-aa4a-9eced8ad61d2" />

#### HTTP
<img width="500" height="688" alt="central11" src="https://github.com/user-attachments/assets/92030b89-5f7d-405a-92f1-c50579a0db2a" />
<img width="500" height="692" alt="central10" src="https://github.com/user-attachments/assets/3c74ab7e-54e4-4892-afa6-ae7fc7ec0d41" />
<img width="500" height="689" alt="central13" src="https://github.com/user-attachments/assets/18895185-de45-46ca-9ef1-e736887ad5c7" />

#### EMAL
  
<img width="325" height="359" alt="2" src="https://github.com/user-attachments/assets/3b43917e-4178-46bc-aaaf-43e34ac8cb3f" />
<img width="500" height="358" alt="1" src="https://github.com/user-attachments/assets/06247531-a49e-407c-a114-e88ca8dae131" />

---

### Lan Rabat 

<img width="600" height="1035" alt="rabat1" src="https://github.com/user-attachments/assets/eaba3bf7-1d81-4e6a-bbc0-9d7c62e28e18" />

#### PC & IP Phone (admin)

<img width="371" height="287" alt="image" src="https://github.com/user-attachments/assets/5c1dd6b3-25ec-4d28-aff2-e2999e21970a" />
<img width="500" height="187" alt="image" src="https://github.com/user-attachments/assets/fdca6a8b-6f26-4ee3-8311-c654aea213d7" />

#### Server DHCP (`192.168.1.254`)

<img width="600" height="539" alt="image" src="https://github.com/user-attachments/assets/ac882169-1e6b-46a7-a22e-f9cb030e3649" />

   <img width="307" height="380" alt="image" src="https://github.com/user-attachments/assets/d7c71dfc-ce3c-450c-bb9c-699958074ebd" />
   <img width="313" height="382" alt="image" src="https://github.com/user-attachments/assets/70ac5eef-7ab6-4753-8a42-3673222926e0" />


#### Server Email (`192.168.1.252`)

<img width="600" height="347" alt="image" src="https://github.com/user-attachments/assets/1418f581-7c5d-433e-a4d0-51439c2a95d8" />
<img width="353" height="359" alt="rabat1" src="https://github.com/user-attachments/assets/63beedaf-ec48-429d-a926-29e54decafdf" />
<img width="500" height="359" alt="rabat2" src="https://github.com/user-attachments/assets/2e4f85a3-189e-4891-a3f9-a5188fb61dce" />
<img width="500" height="359" alt="rabat3" src="https://github.com/user-attachments/assets/8b5b8480-dd64-4d07-8923-0bcd787eb22e" />

#### Server tftp (`192.168.1.253`)

<img width="503" height="252" alt="image" src="https://github.com/user-attachments/assets/119cdf4e-226f-47a7-b2b7-ae9b37fd5246" />


#### HTTP 

<img width="500" height="653" alt="rabat10" src="https://github.com/user-attachments/assets/65e3bd15-4162-4dfa-9592-51f7e9a3eca7" />
<img width="500" height="196" alt="rabat13" src="https://github.com/user-attachments/assets/fd428898-e02f-4067-89d8-2656240fa747" />
<img width="500" height="173" alt="rabat12" src="https://github.com/user-attachments/assets/6faa41bc-13e8-47cb-892a-a36209ba3b38" />

---

### Lan Agadir

<img width="600" height="1203" alt="aga1" src="https://github.com/user-attachments/assets/68ebf2a0-b0b2-4b11-bc1b-f0192b70b111" />

#### PC & IP Phone (admin)

<img width="310" height="284" alt="image" src="https://github.com/user-attachments/assets/7e7475e3-a4dd-47ea-b9e6-221c94a5ee76" />
<img width="500" height="214" alt="image" src="https://github.com/user-attachments/assets/d2e5df3e-e972-4f64-9a89-80d3fcc24cc7" />

#### DHCP (router)

<img width="293" height="423" alt="image" src="https://github.com/user-attachments/assets/c760682d-0226-470c-8b73-b5e9db71766d" />
<img width="500" height="246" alt="image" src="https://github.com/user-attachments/assets/978d9b5e-0a94-4034-890a-160d7c9b187a" />


#### Server Email (`192.168.5.252`)


<img width="500" height="346" alt="image" src="https://github.com/user-attachments/assets/17e80b46-e25d-487b-9484-a4b9c61f17b9" />
<img width="285" height="363" alt="1 aga" src="https://github.com/user-attachments/assets/5222d534-6cb6-44bc-ae23-02958008babb" />
<img width="500" height="328" alt="ag2" src="https://github.com/user-attachments/assets/d4d752f5-9c13-4da2-84c8-cf1b9e062df9" />
<img width="500" height="327" alt="ag3" src="https://github.com/user-attachments/assets/ced1731a-bad9-4fae-9767-9b1eb246eea9" />

#### Server tftp (`192.168.5.254`)

<img width="289" height="246" alt="image" src="https://github.com/user-attachments/assets/641df81f-7a46-45bf-9f0f-402928f984d0" />

---

### Lan Casablanca

<img width="600" height="1233" alt="casa" src="https://github.com/user-attachments/assets/03dd2372-249c-4422-aeda-772495ce109c" />


#### PC & IP Phone (admin)


<img width="373" height="284" alt="image" src="https://github.com/user-attachments/assets/bdc8cb6f-7df6-4b9e-a864-a4932b6e42cf" />
<img width="588" height="207" alt="image" src="https://github.com/user-attachments/assets/545de712-9693-4c3d-bb26-1d50ab548191" />


#### DHCP (mls)

<img width="300" height="425" alt="image" src="https://github.com/user-attachments/assets/f4b5f483-8ab9-4a47-a1b1-7b33b9b64356" />
<img width="500" height="248" alt="image" src="https://github.com/user-attachments/assets/67c7fc3b-aa76-469c-b91c-8dbb4bf2614d" />


#### Server Email (`192.168.90.252`)

<img width="500" height="345" alt="image" src="https://github.com/user-attachments/assets/5aeba182-1eca-49d7-977e-c1fc80808bf1" />
<img width="286" height="409" alt="cas1" src="https://github.com/user-attachments/assets/6373f80e-a3ed-4ee5-bedf-4b0bbc9aacb4" />
<img width="500" height="328" alt="ca2" src="https://github.com/user-attachments/assets/70affd4f-05e2-4967-b955-38ad5642558e" />
<img width="500" height="326" alt="ca3" src="https://github.com/user-attachments/assets/deee3d92-d972-44f0-9066-94b6c4b33135" />


#### Server tftp (`192.168.90.254`)

<img width="500" height="254" alt="image" src="https://github.com/user-attachments/assets/ecdd8a2e-d665-47fc-9608-292f508c1d16" />







## 
---

## 👨‍💻 Auteur

**Mohamed ECH-chafiy**
- Étudiant en Infrastructure Digitale — Option Systèmes & Réseaux  
- OFPPT, Arrahma, Casablanca, Maroc  
- Linkedin : www.linkedin.com/in/mohamed-ech-chafiy
