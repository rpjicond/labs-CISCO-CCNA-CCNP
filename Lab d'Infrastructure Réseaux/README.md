# 🚀 Projet HORIZON: Architecture Réseau d'Entreprise (3-Tier, FortiOS & Zabbix)
> **Déploiement d'une infrastructure hybride Zero-Trust pour centres de données, avec supervision proactive et haute disponibilité.**

Ce projet est une conception et une implémentation d'une architecture réseau d'entreprise complète, modélisée sur un campus d'entreprise de Toulouse. Il intègre des couches de réseau Cisco (Core, Distribution, Accès), une sécurité périmétrique Fortinet et une supervision centralisée avec Zabbix 7.0.

![Cisco](https://img.shields.io/badge/Cisco-Networking-blue.svg)
![Fortinet](https://img.shields.io/badge/Fortinet-Security-red.svg)
![Zabbix](https://img.shields.io/badge/Monitoring-Zabbix%207.0-green.svg)
![HA](https://img.shields.io/badge/HA-HSRP%20%7C%20EtherChannel-lightgrey.svg)
![ZeroTrust](https://img.shields.io/badge/Security-Zero%20Trust-orange.svg)

---

## 🌐 1. Topologie Réseau et Architecture (Vue d'Ensemble)

L'infrastructure suit un modèle hiérarchique **Core-Distribution-Accès (3-Tier)**, garantissant évolutivité, performance et résilience. La sécurité est renforcée par une stratégie **Zero-Trust** à travers les couches.

### Schéma de la Topologie Globale
🖼️ **<img width="1919" height="929" alt="image" src="https://github.com/user-attachments/assets/68696d1a-c629-47e1-825a-f6a705ab2bc3" />
**
> *Légende : Topologie physique du réseau d'entreprise, incluant les couches Core, Distribution, Accès, les Firewalls FortiGate et le routeur WAN.*

### Flux de Communication
Le trafic réseau est rigoureusement contrôlé :
1.  Les **Clients** initient des requêtes vers leurs passerelles sur la couche **Distribution**.
2.  Le trafic inter-VLAN est routé sur la couche **Distribution** ou **Core**.
3.  Tout le trafic destiné à la **WAN (Internet)** ou à la **DMZ** passe impérativement par le cluster **FortiGate**.
4.  Les politiques du FortiGate contrôlent l'accès basé sur les zones de confiance (LAN, DMZ, WAN).

---

## 🛠️ 2. Composants Clés et Fonctions

### A. Couches Réseau Cisco (GNS3)

*   **Core Layer (`TLS-CORE-SW-01/02`)**
    *   **Rôle :** Backbone de commutation et de routage à haute vitesse.
    *   **Redondance :** Liens agrégés **EtherChannel (LACP)** entre les deux switches Core.
    *   **Routage :** OSPFv2 pour la convergence rapide.

*   **Distribution Layer (`TLS-DIST-SW-01/02`)**
    *   **Rôle :** Fournit les passerelles (Gateways) pour toutes les VLANs (Inter-VLAN Routing).
    *   **Haute Disponibilité :** Implémentation du **HSRP (Hot Standby Router Protocol)** pour les passerelles virtuelles.
    *   **Sécurité :** Point d'application des ACLs pour le contrôle inter-VLAN.

*   **Access Layer (`TLS-ACC-SW-01/02/03/04`)**
    *   **Rôle :** Connecte les utilisateurs, serveurs VMware et dispositifs DMZ.
    *   **Sécurité :** Configuration de **Port-Security** et **BPDU Guard** pour la protection des points d'accès.

### B. Sécurité Périmétrique Fortinet (FortiGate Cluster)

*   **Équipement :** Deux FortiGate (VM) en cluster **HA (Haute Disponibilité)** Actif/Passif.
*   **Rôle :** Point d'entrée et de sortie unique pour toutes les zones de confiance (LAN, DMZ, WAN).
*   **Fonctionnalités :**
    *   **Inspection SSL :** Déchiffre le trafic HTTPS pour détecter les menaces cachées.
    *   **SD-WAN :** Optimisation et routage des liens WAN (simulé).
    *   **Politiques Zero-Trust :** Contrôle d'accès granulaire basé sur les zones et les services.

### C. Services d'Infrastructure (VMware)

*   **Hyperviseur :** VMware Workstation (simule un environnement ESXi).
*   **Serveurs :**
    *   `TLS-ZBX-MON-01` (Zabbix 7.0 Server) : **Supervision centralisée** de tous les équipements réseau et serveurs via SNMPv3 et Zabbix Agent.
    *   `TLS-INFRA-SRV-01` (DNS / DHCP / NTP) : Fournit les services d'infrastructure essentiels à toutes les VLANs.
    *   `TLS-AUTH-SRV-01` (OpenLDAP) : Fournit les services d'identité pour les futures intégrations (ex: FortiGate FSSO).

---

## 📋 3. Plan d'Adresses IP et VLANs

Une segmentation réseau stricte est appliquée via des VLANs, avec des passerelles HSRP sur la couche de Distribution.

| ID VLAN | Nom VLAN | Sub-réseau | Passerelle HSRP/FW | Mappage VMware |
| :------ | :------- | :--------- | :----------------- | :------------- |
| **10**  | `USERS`    | `172.16.10.0/24` | `172.16.10.254`    | VMnet10        |
| **20**  | `SERVERS`  | `172.16.20.0/24` | `172.16.20.254`    | VMnet2         |
| **30**  | `ADMIN`    | `172.16.30.0/24` | `172.16.30.254`    | VMnet3         |
| **50**  | `DMZ_WEB`  | `10.0.50.0/24`   | `10.0.50.1` (FortiGate) | VMnet5         |
| **60**  | `GUEST`    | `172.16.60.0/24` | `172.16.60.254`    | VMnet4         |
| **99**  | `MGMT`     | `172.16.99.0/24` | `172.16.99.254`    | N/A            |

---

## 📊 4. Observabilité (Zabbix 7.0)

🖼️ **[INSÉRER ICI LE LIEN VERS VOTRE IMAGE DU DASHBOARD ZABBIX]**
> *Légende : Vue d'un tableau de bord Zabbix affichant l'état des liens et la performance des équipements Cisco et FortiGate.*

*   **Collecte de Données :** Utilisation de **SNMPv3** pour la surveillance des équipements réseau (Cisco, FortiGate) et de **Zabbix Agent 2** pour les serveurs Linux.
*   **Alerting :** Configuration d'alertes pour les pannes de liens, les saturations de CPU/mémoire et les changements d'état HSRP/HA.
*   **Cartes Réseau :** Création de cartes visuelles dans Zabbix pour une représentation en temps réel de l'état de la topologie.

---

## 📂 5. Structure du Dépôt GitHub

```text
Project-HORIZON/
├── 01-Network-Cisco/           # Configurations des équipements Cisco (Core, Dist, Acc, OSPF, HSRP, VLANs)
├── 02-Security-FortiGate/      # Configuration des Firewalls FortiGate (HA, Zones, Politiques, Inspection SSL)
├── 03-Infrastructure-VMware/   # Configurations des VMs (Zabbix, DNS/DHCP, LDAP)
├── 04-Documentation/           # Diagrammes (Mermaid, PNG), plan d'adressage
└── README.md                   # Ce fichier
