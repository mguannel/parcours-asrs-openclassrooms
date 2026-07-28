# Parcours Administrateur systèmes, réseaux et sécurité — OpenClassrooms

R�capitulatif des projets réalisés et validés dans le cadre du parcours
**Administrateur d'infrastructures sécurisées** (OpenClassrooms, titre RNCP niveau 6).

Chaque projet part d'un scénario d'entreprise fictive et donne lieu à des livrables
techniques : dossiers d'architecture, fichiers de configuration, scripts, rapports
d'audit. Ce dépôt en présente la synthèse.

> **Note.** Les livrables bruts contenant des secrets (clés privées, mots de passe,
> rapports d'audit détaillés) ne sont volontairement pas publiés ici. Le dépôt décrit
> le travail réalisé ; les configurations sensibles restent hors ligne. Tous les
> scénarios sont pédagogiques et s'appuient sur des données fictives.

---

## Technologies mises en œuvre au fil du parcours

**Systèmes** — Windows Server 2019/2022, Active Directory (ADDS, GPO, AGDLP), Debian/Ubuntu Server
**Réseau** — VLAN, routage inter-VLAN, DNS (Bind9), DHCP, IPv4/IPv6, NAT, Cisco Packet Tracer
**Sécurité** — pfSense, VPN IPsec, Fail2ban, mod_evasive, TLS/SSL, durcissement ANSSI, pentest AD
**Web** — Apache, Nginx, PHP, MariaDB/MySQL, vsftpd (FTPS)
**Automatisation & supervision** — Ansible, Nagios, Rsyslog, Bash, PowerShell, rsync, cron
**Outillage offensif (audit)** — nmap, CrackMapExec, ldapsearch, enum4linux, Mimikatz, SharpHound, Rubeus

---

## Vue d'ensemble

| # | Projet | Domaine dominant |
|---|--------|------------------|
| 02 | Gérez les demandes au quotidien | Support N1 / ITSM (GLPI) |
| 03 | Réseau local d'une startup | Conception réseau, VLAN, adressage |
| 04 | Architecture n-tiers pour une PME | DNS, web, base de données |
| 05 | Services web sécurisés | Apache/Nginx, TLS, Fail2ban, FTPS |
| 06 | Raccordement d'une entité au SI | Active Directory, GPO, VPN IPsec |
| 07 | Services réseaux et interconnexion | VLAN, DNS, routage Cisco, IPv6 |
| 08 | Supervision du SI | Nagios, Rsyslog, indicateurs |
| 09 | Gestion du parc informatique | Ansible, GLPI, scripts multi-OS |
| 10 | Nouvelle solution de sauvegarde | rsync, cron, incrémental/différentiel |
| 11 | Sécurisation du réseau d'une entreprise | Durcissement ANSSI, DMZ, VPN |
| 12 | Sécurité d'un domaine Windows / AD | Pentest Active Directory, remédiation |
| 13 | Migration vers le cloud | Veille cloud, plan de migration |

---

## 02 — Gérez les demandes au quotidien

**Contexte.** Traitement de tickets de support de niveau 1 dans un outil ITSM.

**Travaux réalisés.**
- Résolution de tickets variés : accès applicatif, raccordement VPN IPsec, procédure de suspicion de machine infectée, réapprovisionnement de disque, gestion de comptes.
- Export de la base de données de l'outil (dump SQL).
- Déploiement de l'**agent GLPI** pour l'inventaire automatique du parc (matériel, logiciel, suivi des changements).
- Formalisation d'un **logigramme du processus de support de niveau 1**.

**Compétences.** Gestion des incidents, inventaire de parc, formalisation de procédures.

**Technologies.** `GLPI`, `GLPI Agent`, `SQL`

📁 [`02-gestion-demandes-quotidien/`](./02-gestion-demandes-quotidien/)

---

## 03 — Réseau local d'une startup

**Contexte.** Rédaction du **Dossier d'Architecture Technique (DAT)** du bâtiment Hill Start, à destination des équipes de déploiement.

**Travaux réalisés.**
- Cartographies : plan du bâtiment, schéma physique, schéma logique.
- **Plan d'adressage segmenté en VLAN** avec calcul des masques et des plages :
  - VLAN 10 Caméras de surveillance, VLAN 20 Contrôleur d'accès, VLAN 30 Wifi, VLAN 40 Imprimantes (`/27` chacun), VLAN 50 Internet (`/23`, ~510 hôtes).
- Adressage détaillé équipement par équipement (serveurs, caméras, postes).

**Compétences.** Conception réseau, VLSM, documentation technique normalisée.

**Technologies.** `VLAN`, `VLSM`, `TCP/IP`

📁 [`03-reseau-local-startup/`](./03-reseau-local-startup/)

---

## 04 — Architecture n-tiers pour une PME

**Contexte.** Déploiement d'une architecture **3-tiers** (BeeSafe) : serveur DNS, serveur web, serveur de base de données, sur le réseau `192.168.100.0/24`.

**Travaux réalisés.**
- **DNS (Bind9)** : zone directe et zone inverse pour `beesafe.co`, enregistrements A/CNAME/PTR, forwarders et restriction des requêtes au LAN.
- **Web (Apache)** : virtual host pour `www.beesafe.co`, gestion des logs.
- **Base de données (MariaDB)** : création d'un utilisateur applicatif avec privilèges restreints (SELECT/INSERT/UPDATE/DELETE) limités à l'hôte web — application du moindre privilège.
- Configuration de la connexion applicative web ↔ base.

**Compétences.** Architecture applicative multi-tiers, séparation des rôles serveur, moindre privilège en base.

**Technologies.** `Bind9`, `Apache`, `PHP`, `MariaDB`

📁 [`04-architecture-n-tiers/`](./04-architecture-n-tiers/)

---

## 05 — Services web sécurisés

**Contexte.** Sécurisation des services web d'une banque fictive (RainbowBank) : extranet et interface d'administration.

**Travaux réalisés.**
- **Virtual hosts Apache et Nginx** avec redirection systématique HTTP → HTTPS.
- **TLS/SSL** : génération de CSR, clés et certificats pour chaque service.
- **Contrôle d'accès** : zones protégées par authentification Basic (`.htpasswd`) et restriction par plage IP sur les répertoires sensibles (`/pdf`).
- **Fail2ban** : jails personnalisées ciblant les scans (motif sur les réponses HTTP 404), plus jails Apache et vsftpd.
- **mod_evasive** : protection anti-DoS (seuil de pages/seconde, période de blocage, notification mail, journalisation).
- **vsftpd en FTPS** : chiffrement forcé des données et de l'authentification, chroot des utilisateurs, plage de ports passifs maîtrisée, désactivation de SSLv2/SSLv3.

**Compétences.** Durcissement de serveurs web, PKI applicative, protection contre le bruteforce et le DoS.

**Technologies.** `Apache`, `Nginx`, `OpenSSL`, `Fail2ban`, `mod_evasive`, `vsftpd`

📁 [`05-services-web-securises/`](./05-services-web-securises/)

---

## 06 — Raccordement d'une entité au SI

**Contexte.** Intégration d'une entité bancaire (sites de Paris et Nantes) au système d'information : Active Directory, postes, partages et interconnexion sécurisée.

**Travaux réalisés.**
- **Active Directory** : structuration en unités d'organisation (Direction, IT, Banque par site), création des utilisateurs et des ordinateurs par OU.
- **GPO** déployées : connexion de lecteurs réseau, restriction des disques amovibles, plages horaires de connexion, déploiement de flux.
- **VPN IPsec site à site** entre Paris et Nantes configuré sur **pfSense** (Phase 1 : AES + SHA-256, DH groupe 14 ; Phase 2 : ESP, tunnel).
- **Script PowerShell** de sauvegarde d'un partage réseau vers un stockage distant via Robocopy (mode miroir, reprise sur erreur, journalisation).

**Compétences.** Administration Active Directory, stratégies de groupe, VPN inter-sites.

**Technologies.** `Active Directory`, `GPO`, `pfSense`, `IPsec`, `PowerShell`, `Robocopy`

📁 [`06-raccordement-entite-si/`](./06-raccordement-entite-si/)

---

## 07 — Services réseaux et équipements d'interconnexion

**Contexte.** Configuration d'une infrastructure multi-sites sous **Cisco Packet Tracer** avec adressage double pile IPv4/IPv6.

**Travaux réalisés.**
- **Plan d'adressage IPv4 + IPv6** couvrant routeurs (liaisons série en `/30`), serveurs et une dizaine de VLAN métier (IT, Finance, Commercial, RH, Direction, Accueil, Showroom…).
- **Routeurs Cisco** : sous-interfaces (routage inter-VLAN), tables de routage, NAT, pools DHCP par VLAN.
- **DNS** et interconnexion des sites (routeurs Bleu / Rouge / Vert).
- Rédaction d'un document de **préconisations de cybersécurité alignées ANSSI** : bastion d'administration (R8), pare-feu secondaire (R5), politique de sauvegarde (R45).

**Compétences.** Routage, segmentation VLAN, double pile IPv6, préconisations ANSSI.

**Technologies.** `Cisco IOS`, `Packet Tracer`, `VLAN`, `IPv6`, `DHCP`, `NAT`

📁 [`07-services-reseaux-interconnexion/`](./07-services-reseaux-interconnexion/)

---

## 08 — Supervision du SI

**Contexte.** Mise en place de la supervision d'une infrastructure sous **Nagios**, avec centralisation des journaux.

**Travaux réalisés.**
- **Sondes Nagios** avec seuils warning/critical : `check_mysql`, `check_cpu_load`, `check_disk`, `check_http`, `check_index` (présence d'une chaîne sur la page), `check_mem` (script custom), `check_nagios_session`.
- **Centralisation des logs** via Rsyslog (configuration client + serveur).
- **Procédure d'exploitation** documentée : conduite à tenir pour chaque type d'alerte (SQL, CPU, disque, HTTP, RAM, sessions), avec commandes de diagnostic (`top`, `ss -tlnp`, `df -h`, `find`…) et actions correctives.

**Compétences.** Supervision, définition d'indicateurs, gestion des logs, procédures d'incident.

**Technologies.** `Nagios`, `Rsyslog`, `Bash`

📁 [`08-supervision-si/`](./08-supervision-si/)

---

## 09 — Gestion du parc informatique d'une PME

**Contexte.** Automatisation de la gestion d'un parc mixte Windows/Linux dans le domaine `barzini.local`, avec inventaire GLPI.

**Travaux réalisés.**
- **Ansible** : inventaire multi-OS (SSH pour Linux, WinRM/NTLM pour Windows) et playbooks de :
  - mise à jour des paquets Linux (`apt`),
  - déploiement d'applicatifs (Kontrast via apt, Colour Contrast Analyser via Chocolatey),
  - montage de partages réseau (Services et dossiers nominatifs) via tâches planifiées.
- **Scripts de montage** : PowerShell (Windows, `New-PSDrive` + journalisation) et Bash (Linux, `mount -t cifs` avec authentification **Kerberos** `sec=krb5`), déclenchés à l'ouverture de session.
- **Inventaire GLPI** : exports du parc (périphériques, utilisateurs) et référentiel.

**Compétences.** Automatisation multi-OS, gestion de configuration, inventaire de parc, montage CIFS/Kerberos.

**Technologies.** `Ansible`, `PowerShell`, `Bash`, `GLPI`, `Chocolatey`, `Kerberos`, `CIFS/SMB`

📁 [`09-gestion-parc-informatique/`](./09-gestion-parc-informatique/)

---

## 10 — Nouvelle solution de sauvegarde

**Contexte.** Mise en place d'une stratégie de sauvegarde de machines virtuelles pour une mairie (Mareuil-sur-Oise), sur la base de **rsync over SSH**.

**Travaux réalisés.**
- **Quatre scripts Bash** couvrant sauvegarde et restauration, en modes **incrémental** et **différentiel**.
- **Sauvegarde incrémentale** multi-dossiers (FICHIERS, MAILS, RH, SITE, TICKETS) avec `--link-dest` sur la sauvegarde J-1 (sauvegardes complètes apparentes, optimisées en espace par liens durs) et gestion de rétention.
- **Résilience** : options `--partial` et `--append` pour la reprise après coupure ; contrôle de fraîcheur des sauvegardes (< 24 h) avant restauration.
- **Automatisation via cron** (incrémental quotidien, différentiel horaire) et journalisation complète.
- Procédure de restauration illustrée sur un incident réel (ticket #127 : restauration d'un `index.html` supprimé).

**Compétences.** Stratégies de sauvegarde, scripting robuste, planification, PRA.

**Technologies.** `rsync`, `SSH`, `Bash`, `cron`

📁 [`10-solution-sauvegarde/`](./10-solution-sauvegarde/)

---

## 11 — Sécurisation du réseau d'une grande entreprise

**Contexte.** Mise à niveau de la sécurité du réseau du département R&D d'Open Pharma (11 personnes) suite à une attaque, selon les recommandations de l'**ANSSI**, avec un budget plafonné.

**Travaux réalisés.**
- **Cartographie** du SI et **documentation applicative** (inventaire des applications et des comptes).
- **Plan projet** : rappel de contexte, devis matériel/logiciel (switch managé, serveur, NAS, licences), chiffrage des ressources humaines, planification.
- **Diagramme de Gantt** du déploiement (étude → déploiement → tests → support).
- **Documentation des mesures de durcissement** mappées aux recommandations ANSSI : bastion d'administration (R8), segmentation VLAN (R19), comptes d'administration dédiés (R27), politique de mots de passe (R34), MFA (R36), certificats (R37), VPN distincts utilisateurs/admin (R49), méthode AGDLP (R40), MCS/DMZ (R42), proxy filtrant, sécurisation des protocoles (HTTPS, LDAPS, SMTPS, IPPS).

**Compétences.** Analyse de risque, référentiel ANSSI, conduite de projet sécurité, chiffrage.

**Technologies.** `pfSense`, `Active Directory`, `VLAN`, `DMZ`, `VPN`, référentiel `ANSSI`

📁 [`11-securisation-reseau-entreprise/`](./11-securisation-reseau-entreprise/)

---

## 12 — Sécurité d'un domaine Windows et de l'Active Directory

**Contexte.** **Test d'intrusion** de l'environnement Active Directory d'une clinique fictive, sur le périmètre autorisé `10.10.10.0/24`, suivi d'un plan de remédiation.

**Travaux réalisés.**
- **Énumération** : découverte réseau (nmap), identification du contrôleur de domaine, énumération des ports (LDAP 389/636), du domaine (`ldapsearch`) et des partages SMB.
- **Compromission initiale** : password spraying / user-as-pass.
- **Reconnaissance interne** : énumération des utilisateurs du domaine (CrackMapExec), exploration des partages, accès RDP.
- **Mouvement latéral et élévation** : Mimikatz, Rubeus, SharpHound, Snaffler ; tickets Kerberos, Pass-the-Hash, Kerberoasting, identification des comptes privilégiés.
- **Maintien d'accès** : dump de la base AD, création d'un compte administrateur.
- **Livrables** : rapport de pentest structuré, tableau de vulnérabilités (V01–V10) avec criticité, **plan d'action court/long terme** priorisé (LDAPS, désactivation LLMNR/NetBIOS, politique de mot de passe + MFA, LAPS contre le Pass-the-Hash, moindre privilège, AGDLP, cloisonnement VLAN), et support de **réunion de restitution**.

**Compétences.** Méthodologie de pentest AD, exploitation, rédaction de rapport, remédiation priorisée.

**Technologies.** `nmap`, `CrackMapExec`, `ldapsearch`, `enum4linux`, `Mimikatz`, `SharpHound`, `Rubeus`, `Active Directory`, `Kerberos`, `LAPS`

📁 [`12-securite-domaine-windows-ad/`](./12-securite-domaine-windows-ad/)

---

## 13 — Migration vers le cloud

**Contexte.** Migration de l'application 3-tiers **Patronus** (Nimbus Corp) vers le cloud : serveurs en fin de support et proches de la saturation.

**Travaux réalisés.**
- **Veille cloud** : modèles de service (IaaS/PaaS/SaaS), cloud public vs privé, avantages/inconvénients, comparatif de trois fournisseurs (AWS, Azure, OVHcloud) et de leurs services pertinents (calcul, base managée, stockage, IAM).
- **Dossier de préparation à la migration** : analyse de l'existant (web Apache, base MySQL, fichiers CIFS/SMB, topologie DMZ/LAN), bénéfices attendus, **analyse de risques** (sécurité des données, continuité, RGPD/localisation, dépendance fournisseur, maîtrise des coûts, latence, montée en compétences), facteurs clés de succès.
- **Plan de migration** en phases (audit → conception → infra cloud → migration données → tests → mise en production → formation → clôture/REX) avec **diagramme de Gantt**.
- **Chiffrage** : choix d'**OVHcloud**, estimation des coûts d'infrastructure et de prestation.

**Compétences.** Veille technologique, cadrage de migration, analyse de risques, estimation budgétaire.

**Technologies.** `Cloud (IaaS/PaaS/SaaS)`, `AWS`, `Azure`, `OVHcloud`, `RGPD`

📁 [`13-migration-cloud/`](./13-migration-cloud/)

---

## Structure du dépôt

```
.
├── README.md
├── 02-gestion-demandes-quotidien/
├── 03-reseau-local-startup/
├── 04-architecture-n-tiers/
├── 05-services-web-securises/
├── 06-raccordement-entite-si/
├── 07-services-reseaux-interconnexion/
├── 08-supervision-si/
├── 09-gestion-parc-informatique/
├── 10-solution-sauvegarde/
├── 11-securisation-reseau-entreprise/
├── 12-securite-domaine-windows-ad/
└── 13-migration-cloud/
```

## Avertissement

Tous les projets s'appuient sur des entreprises et des données fictives fournies dans
le cadre pédagogique de la formation. Aucune donnée réelle, clé privée, mot de passe
ou rapport d'audit sensible n'est présent dans ce dépôt.
