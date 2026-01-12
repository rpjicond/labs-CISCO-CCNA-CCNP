# 🚀 Infrastructure d'Entreprise Hautement Disponible et Sécurisée

## 📝 1. Introduction
Ce projet consiste en la conception et la mise en œuvre d'une infrastructure informatique "Full Stack" simulant un environnement de production réel. L'objectif principal est de garantir la **Haute Disponibilité (HA)**, la **Sécurité Périmétrique** et une **Connectivité Totale** via une résolution de noms (DNS) centralisée.

L'infrastructure repose sur un environnement hybride :
*   **Réseau :** Équipements Cisco (Cœur de réseau) et FortiGate (Sécurité).
*   **Systèmes :** Windows Server 2022 (Identité) et Linux Debian/Ubuntu (Applications & Services).

---

## 🏗️ 2. Architecture Réseau & Redondance

### 2.1 Segmentation par VLANs
| ID | Nom | Sous-réseau | Gateway (FG) | Usage |
| :--- | :--- | :--- | :--- | :--- |
| 10 | **USERS** | 192.168.10.0/24 | 192.168.10.1 | Postes clients et utilisateurs |
| 20 | **SERVERS** | 192.168.20.0/24 | 192.168.20.1 | Cœur des services (AD, Mail, Zabbix) |
| 30 | **ADMIN** | 192.168.30.0/24 | 192.168.30.1 | Postes IT et Jump Hosts d'admin |
| 40 | **VOIP** | 192.168.40.0/24 | 192.168.40.1 | Téléphonie IP (QoS priorisée) |
| 60 | **GUEST** | 172.16.60.0/24 | 172.16.60.1 | Wi-Fi Invités (Accès Internet uniquement) |
| 99 | **MGMT** | 10.0.99.0/24 | 10.0.99.1 | Gestion (Switches, Routers, APs) |

### 2.2 Cœur de Réseau & Haute Disponibilité
*   **FortiGate HA Cluster :** Deux unités en mode **Actif-Passif** assurant la continuité du routage inter-VLAN et de la sécurité.
*   **Cisco EtherChannel (LACP) :** Agrégation de liens entre les switches de cœur pour la redondance et l'augmentation de la bande passante.
*   **Spanning-Tree (Rapid-PVST+) :** Configuration optimisée pour éviter les boucles avec une convergence rapide (< 2s).
*   **Routage Inter-VLAN :** Centralisé sur le FortiGate pour permettre l'inspection UTM (Antivirus/IPS) entre les zones.

---

## 🌐 3. Services de Nommage & Adressage (DNS & DHCP)

### 3.1 Matrice DNS (Connectivité Totale)
Le DNS est le pilier de l'infrastructure. Chaque machine est enregistrée dans l'Active Directory pour permettre le **ping par nom FQDN** entre tous les segments.

**Zone Directe : `corp.local`**
| Nom d'hôte | Adresse IP | Type | Description |
| :--- | :--- | :--- | :--- |
| `srv-ad01.corp.local` | 192.168.20.10 | A / PTR | Contrôleur de domaine (Primaire) |
| `srv-ad02.corp.local` | 192.168.20.11 | A / PTR | Contrôleur de domaine (Secondaire) |
| `srv-fs01.corp.local` | 192.168.20.12 | A | Serveur de Fichiers Windows |
| `lnx-mail01.corp.local`| 192.168.20.51 | A / MX | Serveur de Mail (Zimbra) |
| `lnx-zbx01.corp.local` | 192.168.20.50 | A | Serveur Supervision (Zabbix) |
| `lnx-sto01.corp.local` | 192.168.20.52 | A | Serveur Stockage (NFS/TFTP/FTPS) |
| `fw-cluster.corp.local`| 192.168.99.1 | A | Cluster FortiGate (Management) |
| `sw-core01.corp.local` | 192.168.99.10 | A | Switch Cœur de Réseau |

### 3.2 DHCP & IP Helper
*   **Serveur DHCP :** Centralisé sur Windows Server (SRV-AD01).
*   **DHCP Relay :** Configuré sur le FortiGate via la commande `set dhcp-relay ip 192.168.20.10` pour chaque interface VLAN.
*   **Options DHCP :** 
    *   Option 003 (Gateway) 
    *   Option 006 (DNS : 192.168.20.10)
    *   Option 066 (TFTP Server : 192.168.20.52).

---

## 🪟 4. Infrastructure Windows (Active Directory)

### 4.1 Gestion des Identités (AD DS)
*   **Structure des OUs :** Organisation par départements (Direction, RH, IT, Finance).
*   **Groupes de sécurité :** Gestion des permissions NTFS et accès VPN.
*   **GPO :** Montage automatique des lecteurs réseau et déploiement des agents de sécurité.

### 4.2 Services Bureau à Distance (RDS)
*   **SRV-RDS01 :** Permet aux administrateurs et utilisateurs distants d'accéder aux outils internes via une passerelle sécurisée.

---

## 🐧 5. Services Linux & Collaboration

### 5.1 Zimbra Collaboration (Mail)
*   **LNX-MAIL01 :** Serveur de messagerie complet.
*   **Enregistrements DNS :** MX configuré vers l'IP Linux et SPF/DKIM pour la sécurité des mails.

### 5.2 Supervision (Zabbix)
*   **LNX-ZBX01 :** Centralisation des alertes.
*   **Protocoles :** SNMP v3 pour Cisco/FortiGate et Zabbix Agent pour les serveurs Windows/Linux.

### 5.3 Stockage & FTP (LNX-STO01)
*   **NFS :** Partages pour les backups des serveurs Linux.
*   **TFTP :** Utilisé pour la sauvegarde automatique des fichiers de configuration Cisco.
*   **FTPS :** Serveur FTP sécurisé via TLS pour les transferts de fichiers externes.
*   **Samba :** Membre du domaine AD pour une intégration transparente des fichiers.

---

## 🛡️ 6. Sécurité & Pare-feu

### 6.1 Politiques de Flux (Firewall)
| Source | Destination | Service | Action |
| :--- | :--- | :--- | :--- |
| VLAN_USERS | VLAN_SERVERS | DNS, AD, SMB, HTTPS | **ACCEPT** |
| VLAN_ADMIN | TOUS | SSH, RDP, HTTPS | **ACCEPT** |
| Internet | LNX-MAIL01 | SMTP (25), HTTPS (443) | **ACCEPT (NAT)** |
| VLAN_GUEST | Internet | HTTP, HTTPS | **ACCEPT** |

### 6.2 Sécurité de Commutation (Cisco)
*   **Port-Security :** Limitation des adresses MAC par port utilisateur.
*   **BPDU Guard :** Protection contre l'insertion de switches non autorisés.
*   **Isolation :** Désactivation des ports non utilisés et assignation au VLAN "Dead-End".

---

## 🛠️ 7. Procédures de Test & Validation
Pour valider l'infrastructure, les tests suivants sont réalisés :
1.  **Test DNS :** `ping lnx-mail01.corp.local` depuis un PC client (Vérification de la résolution et du routage).
2.  **Test DHCP :** Vérifier qu'un PC en VLAN 10 reçoit une IP du scope Windows.
3.  **Test Redondance :** Déconnexion d'un lien EtherChannel (Vérification du basculement sans coupure).
4.  **Test Mail :** Envoi et réception d'un mail interne via l'interface Web Zimbra.
5.  **Test Backup :** Exécution d'un `copy running-config tftp` depuis le switch vers LNX-STO01.

---
**Maintenu par :** Équipe Admin Système & Réseau
**Statut :** Opérationnel / En production