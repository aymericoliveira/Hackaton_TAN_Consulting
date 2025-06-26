## 🗺️ Plan du repository
```bash
Copier
Modifier
Hackaton_TAN_Consulting/  
├── README.md                # Présentation principale du lab   
├── Architecture.md          # Description de l’architecture du lab (avec schéma)    
├── OpenVPN_IPSec.md         # Installation d'un VPN Site-to-Site   
├── IDS_IPS_Suricata.md      # Installation d'un IDS/IPS sur les sites de Paris et Marseille avec configuration du cluster actif/passif
├── images/                  # Captures d’écran et schémas    
│   ├── ipsec_img            # Captures d’écran installation & tests VPN IPsec     
│   └── suricata_img         # Captures d’écran installation & tests IDS/IPS  
└── LICENSE                  # Licence MIT  
```


# Introduction
Ce projet est un cas concret inspiré d’une situation réelle en entreprise.
Il a été réalisé dans le cadre d’un hackathon en équipe de six personnes.

# 👨‍🏫 Sujet
L’objectif principal est de mettre en place la haute disponibilité des firewalls et de sécuriser l’infrastructure réseau, notamment l’accès Internet de TAN Consulting.
Détails de l’entreprise : 149 postes au siège de Paris et 19 à l’agence de Marseille.

Dans une première partie, je vais présenter en détail mes missions. Dans un second temps, je présenterai plus brièvement les réalisations des membres de mon équipe, car le projet est dense et s’est déroulé sur une semaine.

# 🎯 Objectifs du projet
L’objectif global de ce projet est de concevoir et de déployer une infrastructure réseau et système sécurisée, répartie sur deux sites : Paris et Marseille.

## Missions réalisées
- **Évaluation des coûts et des délais** :
Chiffrage du matériel nécessaire et estimation du temps d’intervention.

- Configuration des pare-feux :
Mise en place d’un **cluster actif/passif** de pare-feux à Paris pour assurer la haute disponibilité.

*Déploiement des VPN :*

- **VPN site-to-site** entre Paris et Marseille.

- **VPN client-to-site** pour les collaborateurs distants (à Paris).

- **Filtrage des flux réseau** :
Restrictions aux services DNS, HTTP et HTTPS uniquement.

- Mise en place d’un **proxy à Paris** :

- **Accélération de la navigation** via la mise en cache.

- **Blocage de sites web** selon leur catégorie.

- **Journalisation des connexions** web pendant une durée de 6 à 24 mois.

- **Déploiement d’une solution IDS/IPS** :

- **Détection des activités** suspectes.

- **Blocage des connexions** en provenance de zones à risque (Russie, Ukraine, Chine, Corée du Nord).

## **Infrastructure de Marseille :**

Installation d’un pare-feu unique avec reverse proxy pour la publication d’un extranet client.

Blocage de certaines méthodes HTTP, notamment POST.

Hébergement sécurisé via un conteneur LXC sous Proxmox (Apache ou Nginx).

Journalisation de la navigation web pendant 6 mois minimum.

Intégration locale d’une solution IDS/IPS (Suricata ou pfBlockerNG).

Choix de la solution de firewall :
Utilisation de pare-feux open-source : Endian Community Edition, OPNsense ou pfSense.

# ⚙️ Environnement et prérequis
- **Serveur Proxmox PVE**
- **Outils collaboratifs**  
- **Ubuntu ISO**  
- **Windows ISO** (peu importe la version)  
- **ISO pfSense**  

## 🚀 Axes d'amélioration de l'infrastructure
- Mettre en place une solution de sauvegarde centralisée (Rsync, BorgBackup, NAS…).

- Déployer un contrôleur de domaine Active Directory pour centraliser la gestion des droits et des GPO.

- Appliquer une GPO proxy pour forcer la configuration des postes Windows.

- Ajouter un réseau de gestion et de supervision (interfaces admin, monitoring, logs).

- Durcir le serveur web (en-têtes HTTP sécurisés, filtrage des méthodes, passage au HTTPS).

- Centraliser les logs de sécurité avec Graylog ou Prometheus.

- Renforcer l’IDS/IPS avec pfBlockerNG en complément de Suricata.

- Étudier la mise en place d’une DMZ sur le site de Paris pour des services potentiellement exposés.