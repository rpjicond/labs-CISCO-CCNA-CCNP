# 📘 Project HORIZON : Documentation de Design (LLD)

Ceci est la **Documentation Technique Officielle (Low Level Design - LLD)** de votre projet.

En tant qu’**Ingénieur Réseau**, vous devez conserver ce document dans un fichier appelé **README.md** (ou DESIGN-DOC.md) ou dans **Notion**. C’est la **« Bible » de votre réseau**. Sans elle, vous vous perdez au moment de configurer les IP.
![Uploading image.png…]()

---

## 1. Standard de Nommage

**Code du site** : TLS (Toulouse)

**Format** :

```
[toulouse]-[DG]-[TY001]-[23]
```

**Exemple** :

* `TLS-CORE-SW-01` (Switch Core 01 de Toulouse)

---

## 2. Table des VLANs et Mappage VMware

Cette table est **le secret** pour faire communiquer **GNS3** avec **VMware**.

| ID VLAN | Nom VLAN | Sous-réseau (CIDR) | Passerelle (HSRP/FW) | VMware VMnet | Fonction                              |
| ------: | -------- | ------------------ | -------------------- | ------------ | ------------------------------------- |
|      10 | USERS    | 172.16.10.0/24     | .254                 | VMnet10      | Réseau Utilisateurs / Wi‑Fi Corporate |
|      20 | SERVERS  | 172.16.20.0/24     | .254                 | VMnet2       | Data Center (LDAP, Zabbix, DNS)       |
|      30 | ADMIN    | 172.16.30.0/24     | .254                 | VMnet3       | Réseau de Gestion / PC Admin          |
|      60 | GUEST    | 172.16.60.0/24     | .254                 | VMnet4       | Wi‑Fi Visiteurs (Isolé)               |
|      70 | DMZ_WEB  | 10.0.70.0/24       | .1 (FortiGate)       | VMnet5       | Serveurs Publics (Bordure)            |
|      99 | MGMT     | 172.16.99.0/24     | .254                 | N/A          | Gestion des Switches / Routeurs       |

---

## 3. Inventaire des Serveurs (VMware)

Configurez les cartes réseau de vos VMs **exactement comme ci‑dessous** :

| Hostname         | Fonction             | IP Statique (Linux) | VLAN Cible | Configuration VMware |
| ---------------- | -------------------- | ------------------- | ---------- | -------------------- |
| TLS-AUTH-SRV-01  | OpenLDAP / Identité  | 172.16.20.10        | VLAN 20    | Custom : VMnet2      |
| TLS-INFRA-SRV-01 | DNS / DHCP / NTP     | 172.16.20.11        | VLAN 20    | Custom : VMnet2      |
| TLS-FILE-SRV-01  | Serveur de Fichiers  | 172.16.20.12        | VLAN 20    | Custom : VMnet2      |
| TLS-ZBX-SRV-01   | Serveur Zabbix       | 172.16.20.13        | VLAN 20    | Custom : VMnet2      |
| TLS-ADM-WK-01    | PC Admin (Win/Linux) | 172.16.30.10        | VLAN 30    | Custom : VMnet3      |
| TLS-EDGE-GW-01   | Proxy / Web DMZ      | 10.0.70.10          | VLAN 70    | Custom : VMnet5      |

---

## 4. Inventaire Réseau (GNS3)

Adresses IP de gestion (**Loopback0**) pour l’accès **SSH** et le monitoring **Zabbix**.

| Hostname       | Modèle       | Loopback0 (IP Mgmt) | Fonction                     |
| -------------- | ------------ | ------------------- | ---------------------------- |
| TLS-FW-EDGE-01 | FortiGate VM | 10.255.0.1          | Firewall Principal (Actif)   |
| TLS-FW-EDGE-02 | FortiGate VM | 10.255.0.2          | Firewall Secondaire (Passif) |
| TLS-CORE-SW-01 | Cisco L3     | 10.255.0.3          | Backbone Primaire            |
| TLS-CORE-SW-02 | Cisco L3     | 10.255.0.4          | Backbone Secondaire          |
| TLS-DIST-SW-01 | Cisco L3     | 10.255.0.5          | Passerelle HSRP Master       |
| TLS-DIST-SW-02 | Cisco L3     | 10.255.0.6          | Passerelle HSRP Backup       |
| TLS-ACC-SW-xx  | Cisco L2     | 10.255.0.x          | Accès (VLAN 99)              |

---

## ✅ Ce que vous devez faire MAINTENANT

### Dans VMware (Virtual Network Editor)

* Assurez‑vous que les **VMnet 2, 3, 4, 5 et 10** sont créées conformément à la **table 2**.

### Dans les VMs (Settings)

* Pour chaque machine virtuelle, modifiez la carte réseau vers la **VMnet correcte** selon la **table 3**.

  * Exemple : `FILE-SRV-01` → **VMnet2**

### Dans GNS3 (Clouds)

* Faites un clic droit sur les icônes de **nuage (vSwitch)**
* Configurez :

  * Nuage connecté à **ACC-SW-01** → Ajouter l’interface **VMnet10**
  * Nuage connecté à **ACC-SW-02 (Serveurs)** → Ajouter l’interface **VMnet2**
  * Nuage connecté à **ACC-SW-04 (Admin)** → Ajouter l’interface **VMnet3**
  * Nuage de la **DMZ** → Ajouter l’interface **VMnet5**

---

## ⏭️ Prochaine Étape

Maintenant que la documentation est prête et que les VMs sont **connectées aux bons réseaux**, il faut faire **circuler le sang dans les veines du réseau**.

### Configuration du « Cœur Cisco »

* Démarrer les **switches Core et Distribution** dans GNS3
* Configurer les **trunks** entre eux
* Créer les **VLANs** dans la base de données des switches
* Configurer le **HSRP** (pour que **172.16.20.254** existe et que vos serveurs aient accès à Internet)

---

📌 **Ce document doit toujours être ouvert pendant vos configurations.**
Il est votre référence absolue.
