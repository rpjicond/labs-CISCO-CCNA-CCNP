# III. Services Systèmes (Windows & Linux)

## 3.1 Infrastructure Windows
Le domaine `corp.local` est le pilier de la gestion des utilisateurs.
*   **SRV-AD01 / 02 :** Contrôleurs de domaine, DNS intégré et serveurs DHCP (avec IP Helper).
*   **SRV-FS01 :** Serveur de fichiers avec gestion des quotas et droits NTFS via groupes AD.
*   **SRV-RDS01 :** Serveur de bureau à distance pour l'administration sécurisée.

## 3.2 Services Linux
*   **LNX-MAIL01 (Zimbra) :** Solution de messagerie complète (SMTP, IMAP, Webmail) intégrée au DNS interne.
*   **LNX-ZBX01 (Zabbix) :** Supervision centralisée via SNMP (Cisco/FortiGate) et agents (Windows/Linux).
*   **LNX-STO01 (Storage) :**
    *   **NFS :** Partages pour les backups Linux.
    *   **TFTP :** Sauvegarde des configurations des switches Cisco.
    *   **FTPS :** Transfert de fichiers sécurisé via TLS.