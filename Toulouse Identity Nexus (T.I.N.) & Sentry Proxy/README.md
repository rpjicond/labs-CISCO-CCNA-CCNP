# 🚀 Toulouse Identity Nexus (T.I.N.)
> **Advanced Zero-Trust Network Infrastructure & Automated Identity Management System.**

Ce projet simule une infrastructure réseau d'entreprise complète et sécurisée, déployée sous Linux (Debian 12). Il met en œuvre une gestion centralisée des identités via OpenLDAP et un contrôle d'accès strict par Proxy authentifié.

---

## 🌐 1. Architecture Réseau (Network Topology)

L'infrastructure est segmentée pour garantir une sécurité maximale. Aucun serveur interne n'est exposé directement à l'internet (WAN).

### 🖼️ [INSERIR AQUI A IMAGEM DO ESQUEMA QUE O GEMINI GEROU]
> *Légende : Topologie Triple-Layer (Gateway, Auth Server, Proxy Server).*

---

## 🛠️ 2. Composants du Projet

### A. Le "Nexus CLI" (Gestion des Identités)
Un outil d'administration avancé écrit en **Bash** qui automatise la gestion des utilisateurs LDAP.
*   **Automatisation :** Génération de logins (ex: n.tesla), calcul d'UID incrémentiel.
*   **Sécurité :** Hachage SSHA des mots de passe.
*   **Interface :** Design moderne avec animations (spinners) et codes couleurs ANSI.

### 🖼️ [INSERIR AQUI O PRINT DO MENU AZUL DO NEXUS_LDAP.SH]
> *Capture d'écran de l'interface Nexus CLI en action.*

---

### B. Le "Sentry Proxy" (Contrôle d'Accès)
Serveur **Squid Proxy** durci, agissant comme le gardien du réseau.
*   **Zéro-Anonymat :** Authentification obligatoire via le serveur LDAP de Toulouse.
*   **Filtrage :** Blacklistage de domaines (ACLs) pour interdire les réseaux sociaux.
*   **Observabilité :** Dashboard de monitoring en temps réel développé sur mesure.

### 🖼️ [INSERIR AQUI O PRINT DA TABELA DE MONITORAMENTO (VERDE/VERMELHO)]
> *Dashboard Sentry affichant les flux autorisés (Google) et bloqués (Facebook).*

---

## 💻 3. Stack Technique

| Technologie | Usage |
| :--- | :--- |
| **OS** | Debian 12 (Bookworm) |
| **Réseau** | Iptables (NAT/Routing), VMware LAN Segments |
| **Identité** | OpenLDAP (slapd) |
| **Proxy** | Squid 5.7 |
| **Scripting** | Bash Advanced (Awk, Sed, Pipes, Stdbuf) |

---

## 🚀 4. Fonctionnement du Système

1.  **Routage :** Le `srv-gateway-01` reçoit le trafic et le redirige vers le LAN via des règles `MASQUERADE`.
2.  **Identité :** Le serveur `srv-auth-01` stocke les comptes utilisateurs POSIX.
3.  **Filtrage :** Lorsqu'un utilisateur tente de naviguer, le `srv-proxy-01` intercepte la requête.
4.  **Audit :** Chaque accès est logué avec succès (200 OK) ou échec (403 Forbidden) dans le tableau de bord Sentry.

---

## 📂 5. Structure du Dépôt

```text
.
├── nexus_ldap.sh      # Script CLI de gestion LDAP (srv-auth)
├── nexus_sentry.sh    # Script de monitoring temps réel (srv-proxy)
├── structure.ldif     # Arborescence initiale de l'annuaire
├── squid.conf         # Configuration durcie du Proxy
└── img/               # Captures d'écran et schémas