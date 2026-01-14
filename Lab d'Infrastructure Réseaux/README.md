# 🚀 Infrastructure d'Entreprise : Conception et Déploiement (Full-Mesh HA)

## 📝 1. Introduction
Ce projet représente la conception et la documentation d'une infrastructure réseau et systèmes de classe "Mission-Critical". L'architecture vise une **tolérance aux pannes absolue (Zero Single Point of Failure)** grâce à un **Cœur de réseau en maillage total (Full-Mesh Core)** et un **Cluster de Pare-feu redondant**. L'objectif est de garantir une disponibilité de service de 99.99% pour toutes les applications métiers.

---

## 🏗️ 2. Architecture Réseau (Le Backbone de Haute Performance)

L'infrastructure est composée de **12 équipements réseau actifs** (3 Routeurs de Bordure, 2 Firewalls, 4 Commutateurs Cœur, 3 Commutateurs d'Accès, 1 Commutateur DMZ, 1 Commutateur VoIP) configurés pour une résilience maximale.

### 2.1 Couche Bordure & Sécurité (Full-Mesh HA)
*   **Routeurs de Bordure (EDGE-01, EDGE-02, EDGE-03) :**
    *   **EDGE-01 & EDGE-02 :** Assurent la terminaison WAN/Internet et se connectent au Cluster FortiGate.
    *   **EDGE-03 :** Sert de routeur de distribution interne pour des services spécifiques ou comme point d'agrégation.
*   **Cluster FortiGate (FG-01 & FG-02) :** 
    *   **Redondance Périmétrique :** Les Routeurs de Bordure se connectent aux deux firewalls.
    *   **Redondance vers le Cœur (Full-Mesh HA) :** Chaque firewall est connecté aux deux cœurs de réseau du haut (`CORE-01` et `CORE-03` pour l'image).
    *   *Objectif :* Maintenir la connectivité même en cas de panne simultanée d'un lien WAN, d'une unité de pare-feu et d'un switch de cœur.

### 2.2 Le Cœur de Réseau (Core Quad Mesh)
Le cœur du réseau est un maillage de **4 commutateurs Cisco Multicouches (CORE-01 à CORE-04)** :
*   **Maillage Total (Full-Mesh) :** Tous les commutateurs de cœur sont interconnectés via des agrégats de liens **LACP (Port-Channel)**.
*   **Protocoles :** **Rapid-PVST+** (Spanning Tree) pour la gestion des boucles et un routage L3 distribué.

### 2.3 Couche d'Accès (Distribution Départementale)
Quatre commutateurs Cisco dédiés, connectés en **Dual-Homing** vers le bloc de Cœur :
*   **SW-01 (Accès Mixte) :** Connectivité pour `VLAN 10 (UTILISATEURS)` et `VLAN 60 (INVITÉS)`.
*   **SW-02 (DMZ / Serveurs) :** Dédié à la `VLAN 20 (SERVEURS)` et à la `VLAN 50 (DMZ)`.
*   **SW-03 (Accès Administratif) :** Gère la `VLAN 30 (ADMINISTRATION)` et `VLAN 99 (MANAGEMENT)`.
*   **SW-04 (Réserve/Expansion) :** Commutateur pour l'expansion future ou pour les `VLAN 10/40` supplémentaires.

### 2.4 VLANs & Adressage IP
| VLAN | Nom | Sous-réseau | Rôle | Connecté à |
| :--- | :--- | :--- | :--- | :--- |
| 10 | UTILISATEURS | 192.168.10.0/24 | Postes clients, Wi-Fi interne | SW-01, SW-04 |
| 20 | SERVEURS | 192.168.20.0/24 | Serveurs internes (AD, Zimbra, Zabbix) | SW-02 |
| 30 | ADMINISTRATION | 192.168.30.0/24 | Postes IT et Jump Hosts | SW-03 |
| 40 | VOIP | 192.168.40.0/24 | Téléphonie IP (QoS) | SW-01, SW-04 |
| 50 | DMZ | 192.168.50.0/24 | Serveurs publics (Web, Proxy Mail) | SW-02 |
| 60 | INVITÉS | 172.16.60.0/24 | Wi-Fi Visiteurs (Accès Internet uniquement) | SW-01 |
| 99 | MANAGEMENT | 10.0.99.0/24 | Gestion des équipements | Tous les Switches, Routeurs, Firewalls |

---

## 🌐 3. Services de Nommage & Connectivité (DNS/DHCP)

### 3.1 Matrice DNS (Connectivité Totale par FQDN)
La résolution DNS est centralisée sur l'Active Directory (`corp.local`).

| Hostname | Adresse IP | Service / Rôle |
| :--- | :--- | :--- |
| **rtr-edge.corp.local** | 192.168.99.254 | Routeur Cisco de Bordure |
| **fw-cluster.corp.local**| 192.168.99.1 | IP Virtuelle du Cluster FortiGate |
| **srv-ad01.corp.local** | 192.168.20.10 | AD DS / DNS Maître / DHCP |
| **lnx-mail01.corp.local**| 192.168.20.51 | Messagerie Zimbra (MX Record) |
| **lnx-zbx01.corp.local** | 192.168.20.50 | Supervision Zabbix Server |
| **lnx-sto01.corp.local** | 192.168.20.52 | Stockage (NFS / TFTP / FTPS) |
| **dmz-web01.corp.local** | 192.168.50.10 | Serveur Web (DMZ) |

### 3.2 Relais DHCP (IP Helper)
Le FortiGate agit comme **DHCP Relay** pour tous les VLANs, redirigeant les requêtes vers `SRV-AD01` (VLAN 20).

---

## 🖥️ 4. Inventaire des Machines Virtuelles & Clients

### 4.1 Machines Virtuelles (Serveurs)
| VM Hostname | OS | Fonctions Principales | VLANs Associées |
| :--- | :--- | :--- | :--- |
| **SRV-AD01** | Windows Server | Active Directory, DNS, DHCP, RDP (Jump Server) | 20, 30 |
| **SRV-FS01** | Windows Server | File Server (Samba/SMB), Partages réseau | 20 |
| **LNX-ZBX01** | Linux | Zabbix Server, Syslog, SNMP (Monitoring réseau) | 20, 99 |
| **LNX-SRV01** | Linux | Zimbra (Webmail), VPN Gateway, Reverse Proxy (NGINX), TFTP/FTPS/NFS | 20, 70 (WEB), 80 (VPN Gateway) |
| **DMZ-WEB01** | Linux | Serveur Web (NGINX/Apache), Proxy Mail | 50 (DMZ) |

### 4.2 Clients (Postes Utilisateurs)
| Client Hostname | Système | Fonction | VLAN | Switch de Connexion |
| :--- | :--- | :--- | :--- | :--- |
| **LNXCLIENT-01** | Linux | Client utilisateur standard | 10 | SW-01 |
| **LNXCLIENT-02** | Linux | Second client utilisateur pour tests parallèles | 10 | SW-01 |
| **PC-ADMIN01** | Windows | Poste d'administration réseau | 30 | SW-03 |

---

## 🛡️ 5. Sécurité & Hardening
*   **Segmentation :** Isolation stricte de la DMZ et des VLANs.
*   **Sécurité Portuaire :** BPDU Guard, Port-Security et Dynamic ARP Inspection activés.
*   **VPN SSL :** Accès distant sécurisé pour les administrateurs lié à l'Active Directory (LDAP).

---

## 🛠️ 6. Plan de Validation (Stress Test)
1.  **Test Redondance Firewall :** Provoquer la panne de `FG-01`. Vérifier la continuité du service Internet et DMZ.
2.  **Test Redondance Core :** Éteindre `CORE-01` et `CORE-04`. Confirmer que le trafic USERS <-> SERVERS et DMZ <-> SERVERS continue via les cœurs restants.
3.  **Résolution DNS :** Ping réussi par nom FQDN depuis chaque segment du réseau.
4.  **Audit Zabbix :** Confirmer que l'état des liens redondants est correctement supervisé.
