# 🚀 Toulouse Identity Nexus (T.I.N.)
> **Advanced Zero-Trust Network Infrastructure & Automated Identity Management System.**

Ce projet simule une infrastructure réseau d'entreprise complète et sécurisée, déployée sous Linux (Debian 12). Il met en œuvre une gestion centralisée des identités via OpenLDAP et un contrôle d'accès strict par Proxy authentifié.

---

## 🌐 1. Architecture Réseau (Network Topology)

L'infrastructure est segmentée pour garantir une sécurité maximale. Aucun serveur interne n'est exposé directement à l'internet (WAN).

### <img width="776" height="762" alt="Captura de ecrã 2026-01-24 230640" src="https://github.com/user-attachments/assets/b77ffeaa-0ef2-4de1-a66e-962cddac9edc" />

> *Légende : Topologie Triple-Layer (Gateway, Auth Server, Proxy Server).*

---

## 🛠️ 2. Composants du Projet

### A. Le "Nexus CLI" (Gestion des Identités)
Un outil d'administration avancé écrit en **Bash** qui automatise la gestion des utilisateurs LDAP.
*   **Automatisation :** Génération de logins (ex: n.tesla), calcul d'UID incrémentiel.
*   **Sécurité :** Hachage SSHA des mots de passe.
*   **Interface :** Design moderne avec animations (spinners) et codes couleurs ANSI.

### <img width="1053" height="630" alt="Captura de ecrã 2026-01-24 232517" src="https://github.com/user-attachments/assets/73df75a7-b334-469f-acb2-b67045989927" />

> *Capture d'écran de l'interface Nexus CLI en action.*

---

### B. Le "Sentry Proxy" (Contrôle d'Accès)
Serveur **Squid Proxy** durci, agissant comme le gardien du réseau.
*   **Zéro-Anonymat :** Authentification obligatoire via le serveur LDAP de Toulouse.
*   **Filtrage :** Blacklistage de domaines (ACLs) pour interdire les réseaux sociaux.
*   **Observabilité :** Dashboard de monitoring en temps réel développé sur mesure.

### <img width="1038" height="620" alt="Captura de ecrã 2026-01-24 232335" src="https://github.com/user-attachments/assets/f9c4a234-ed99-4ac9-a4fa-a2d7e10c6623" />

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
