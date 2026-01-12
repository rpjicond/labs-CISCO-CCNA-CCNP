# IV. Sécurité et Politiques de Flux

## 4.1 Politiques de Pare-feu (FortiGate)
Les règles de flux suivent le principe du "moindre privilège" :
*   **LAN to DMZ :** Autorise uniquement HTTPS (443) et SMTP (25).
*   **USERS to SERVERS :** Accès limité au protocole SMB (445), DNS (53) et authentification AD.
*   **ADMIN to ANY :** Accès complet via protocoles sécurisés (SSH, RDP).

## 4.2 Sécurisation des Accès Distants
*   **VPN SSL :** Mis en place sur le FortiGate pour permettre aux administrateurs d'accéder au réseau MGMT depuis l'extérieur.
*   **Authentification :** Liaison du VPN avec l'Active Directory (LDAP).

## 4.3 Sécurité de Commutation (Cisco)
*   **BPDU Guard :** Activé sur les ports d'accès pour éviter les boucles accidentelles.
*   **Port-Security :** Limitation du nombre d'adresses MAC par port utilisateur.