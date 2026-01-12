# 🚀 Infrastructure Réseau & Systèmes d'Entreprise (Lab Haute Disponibilité)

## 📝 Présentation du Projet
Ce projet consiste en la mise en place d'une infrastructure informatique d'entreprise complète et résiliente. L'architecture simule un environnement de production réel intégrant des solutions **Cisco**, **Fortinet**, **Windows Server** et **Linux**.

L'accent est mis sur la **Haute Disponibilité (HA)**, la **segmentation réseau (VLANs)** et la **sécurité périmétrique**.

---

## 🏗️ 1. Architecture Réseau & Cœur de Réseau

### 1.1 Redondance et Performance
Pour garantir un temps d'arrêt minimal, l'infrastructure repose sur :
*   **Cluster Firewall :** Deux unités **FortiGate** configurées en **Haute Disponibilité (Mode Actif-Passif)**.
*   **Cœur de Réseau Cisco :** Utilisation de l'agrégation de liens **LACP (EtherChannel)** entre les commutateurs Core et Distribution pour doubler la bande passante et assurer la redondance physique.
*   **STP (Spanning Tree) :** Configuration de **Rapid-PVST+** pour prévenir les boucles tout en assurant une convergence réseau ultra-rapide.

### 1.2 Segmentation du Réseau (VLANs)
| VLAN | Nom | Sous-réseau | Passerelle | Usage |
| :--- | :--- | :--- | :--- | :--- |
| 10 | **USERS** | 192.168.10.0/24 | .1 | Postes de travail clients |
| 20 | **SERVERS** | 192.168.20.0/24 | .1 | Cœur des services (AD, Mail, Zabbix) |
| 30 | **ADMIN** | 192.168.30.0/24 | .1 | Postes d'administration (Jump Hosts) |
| 40 | **VOIP** | 192.168.40.0/24 | .1 | Téléphonie IP |
| 60 | **GUEST** | 172.16.60.0/24 | .1 | Wi-Fi Visiteurs (Accès Internet uniquement) |
| 99 | **MGMT** | 10.0.99.0/24 | .1 | Gestion des switches et APs |

---

## 🖥️ 2. Inventaire des Services Systèmes

### 🪟 Infrastructure Windows (Active Directory)
*   **Domaine :** `corp.local`
*   **SRV-AD01 / SRV-AD02 :** Contrôleurs de domaine redondants (AD DS), DNS intégré et serveurs DHCP centralisés (via *IP Helper-address*).
*   **SRV-FS01 :** Serveur de fichiers avec gestion des quotas et partages départementaux.
*   **SRV-RDS01 :** Serveur de bureau à distance pour l'accès aux outils d'administration.

### 🐧 Services Linux Critiques
*   **LNX-MAIL01 (Zimbra) :** Serveur de messagerie et de collaboration (SMTP, IMAP, Webmail).
*   **LNX-ZBX01 (Zabbix) :** Supervision proactive via **SNMP** (Cisco/FortiGate) et **Agents** (Windows/Linux).
*   **LNX-STO01 (Stockage & Transfert) :**
    *   **NFS :** Stockage réseau pour les sauvegardes Linux.
    *   **TFTP :** Sauvegarde automatisée des configurations des équipements Cisco.
    *   **FTPS :** Serveur de fichiers sécurisé via TLS pour les échanges externes.
*   **LNX-MGMT01 :** Passerelle SSH (Bastion) et serveur de logs centralisé (Syslog).

---

## 🛡️ 3. Stratégie de Sécurité

### 3.1 Politiques de Firewall (FortiGate)
Le routage inter-VLAN est centralisé sur le FortiGate pour permettre une inspection UTM complète :
*   **Filtrage Granulaire :** USERS vers SERVERS limité aux protocoles nécessaires (AD, SMB, DNS).
*   **Isolation Guest :** Le VLAN 60 n'a aucune communication avec les autres VLANs.
*   **VPN SSL :** Accès distant sécurisé pour les administrateurs avec authentification AD (LDAP).

### 3.2 Sécurité de la Couche 2 (Cisco)
*   **BPDU Guard & Root Guard :** Protection de la topologie Spanning-Tree.
*   **Port-Security :** Limitation du nombre d'adresses MAC sur les ports d'accès utilisateurs.
*   **VLAN natif :** Sécurisation des trunks en changeant le VLAN par défaut (VLAN 1).

---

## 🚀 4. Guide d'Implémentation

### Phase 1 : Infrastructure Réseau Base
1. Configuration des VLANs, VTP et Trunks sur les switches Cisco.
2. Mise en place de l'**EtherChannel (LACP)**.
3. Configuration du cluster HA FortiGate et des sous-interfaces (Gateways).

### Phase 2 : Cœur Windows
1. Déploiement du premier contrôleur de domaine (AD DS).
2. Configuration des étendues DHCP et pointage DNS vers l'AD.
3. Jointure des serveurs membres et des postes clients au domaine.

### Phase 3 : Services Linux & Supervision
1. Installation de **Zimbra** (Configuration des enregistrements DNS MX et SPF).
2. Déploiement de **Zabbix** et configuration des tableaux de bord de supervision.
3. Activation des services de stockage (NFS/TFTP) et scripts de sauvegarde.

---

## 🔑 5. Informations d'Accès (Lab)
*   **Domaine :** `corp.local`
*   **Administrateur :** `administrator@corp.local`
*   **Serveur Zabbix :** `http://192.168.20.50/zabbix`
*   **Webmail :** `https://mail.corp.local`
*   **Gestion Réseau :** Accessibles via SSH sur le VLAN 99.

---
> **Note :** Ce projet est un environnement de laboratoire simulant des standards industriels de haute performance.