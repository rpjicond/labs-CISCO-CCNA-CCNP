# II. Architecture Réseau et Haute Disponibilité

## 2.1 Topologie Logique
L'architecture adopte un design hiérarchique avec un cœur de réseau redondant connecté à un cluster de firewalls.

## 2.2 Plan d'Adressage IP & VLANs
| VLAN | Nom | Sous-réseau | Usage |
| :--- | :--- | :--- | :--- |
| 10 | USERS | 192.168.10.0/24 | Postes clients |
| 20 | SERVERS | 192.168.20.0/24 | Serveurs Windows/Linux |
| 30 | ADMIN | 192.168.30.0/24 | Postes d'administration |
| 99 | MGMT | 10.0.99.0/24 | Gestion des équipements |

## 2.3 Redundance et Haute Disponibilité (HA)
*   **Cœur de Réseau :** Utilisation de deux switches Cisco avec agrégation de liens **LACP (EtherChannel)** pour éviter les points de rupture uniques.
*   **Firewall HA :** Cluster FortiGate en mode **Actif-Passif**.
*   **Spanning-Tree :** Configuration de **Rapid-PVST+** pour une convergence rapide et une protection contre les boucles.

## 2.4 Routage
Le routage inter-VLAN est centralisé sur le FortiGate (**Router-on-a-Stick**), permettant une inspection de sécurité (UTM) entre chaque zone du réseau.