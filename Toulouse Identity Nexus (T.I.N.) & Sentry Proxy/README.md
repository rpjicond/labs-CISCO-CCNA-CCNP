# 🚀 Toulouse Identity Nexus (T.I.N.)
> **Advanced Zero-Trust Network Infrastructure & Automated Identity Management System.**

This project demonstrates a professional, enterprise-grade Linux network deployment. It focuses on high-security standards, centralized identity management, and real-time observability. Designed for a simulated corporate environment in Toulouse, France.

![Linux](https://img.shields.io/badge/OS-Debian%2012-blue.svg)
![Bash](https://img.shields.io/badge/Script-Bash%20Advanced-orange.svg)
![Security](https://img.shields.io/badge/Security-Authenticated%20Proxy-red.svg)
![LDAP](https://img.shields.io/badge/Identity-OpenLDAP-green.svg)

---

## 🌐 Network Architecture

The infrastructure follows a strict **Zero-Trust** approach, segmenting the network into two zones to eliminate single points of failure and unauthorized lateral movement.

### Topology Diagram
![Network Diagram](./img/network_diagram.png) 
*(Note: Replace with your image path or use the Mermaid code provided in the docs)*

### Node Configuration
| Node | Hostname | Internal IP | Role |
| :--- | :--- | :--- | :--- |
| **Gateway** | `srv-gateway-01` | `10.0.0.1` | Linux Router, NAT, Iptables Firewall |
| **Auth** | `srv-auth-01` | `10.0.0.10` | OpenLDAP Provider & Nexus Identity CLI |
| **Proxy** | `srv-proxy-01` | `10.0.0.30` | Sentry Squid Proxy & Real-time Monitor |

---

## 🛠️ Key Components

### 1. Nexus Identity CLI (`nexus_ldap.sh`)
A custom-engineered Bash tool for identity provisioning. It abstracts the complexity of LDAP (LDIF) into a professional "Cyber-style" command-line interface.
*   **Automated POSIX Provisioning:** Calculates next available UIDs and normalizes usernames.
*   **Cryptographic Security:** Implements SSHA password hashing for all directory entries.
*   **Operational UX:** Built-in dependency checking, background processing with spinners, and ANSI color-coded status reporting.

### 2. Sentry Proxy Guard (`nexus_sentry.sh`)
An authenticated Squid Proxy gateway that enforces strict web access policies.
*   **LDAP Integration:** All users must authenticate against the Toulouse LDAP tree before gaining web access.
*   **Content Filtering:** Domain-based Access Control Lists (ACLs) to block high-risk or non-work-related domains.
*   **Live Audit Dashboard:** A real-time monitoring tool that decodes raw proxy logs into a human-readable security feed.

---

## 💻 Tech Stack
*   **Core:** Debian 12 (Bookworm).
*   **Networking:** Iptables (Stateful Packet Inspection & NAT), VMware LAN Segments.
*   **Identity:** OpenLDAP (slapd).
*   **Proxy:** Squid 5.7.
*   **Advanced Scripting:** Bash (Multiprocessing, Awk/Sed parsing, Stream Buffering control).

---

## 🚀 Deployment & Demo

### Administering Identities
To manage users, run the Nexus CLI on `srv-auth-01`:
```bash
sudo ./nexus_ldap.sh