# Proxy transparent

## Objectif

L’objectif du proxy Squid sur le site de Paris est de contrôler et d’optimiser l’accès des utilisateurs à Internet.

D’une part, Squid permet de mettre en place un cache web pour accélérer la navigation en stockant localement les contenus fréquemment consultés, réduisant ainsi la consommation de bande passante.

D’autre part, il permet de réaliser un **filtrage par catégories** de sites (via SquidGuard), conformément aux politiques de l’entreprise (blocage des sites non professionnels, malveillants ou à risque).

Enfin, Squid assure une journalisation complète des accès Internet, garantissant la traçabilité des connexions et permettant de respecter les recommandations de la **CNIL** en matière de conservation des logs.

Il contribue ainsi à améliorer à la fois la performance, la sécurité et la conformité de l’usage d’Internet au sein de l’entreprise.


# Squid

### Installation

Je me suis connecté à l’interface d’administration de pfSense. Depuis le menu **System > Package Manager**, j’ai accédé à l’onglet **Available Packages** pour rechercher le paquet **Squid**. Une fois trouvé, je l’ai installé via le bouton **Install**. À la fin, un message de confirmation m’a indiqué que l’installation était réussie.



### Configuration de Squid (Proxy)

La configuration de Squid se fait dans le menu **Services > Squid Proxy Server**.

![Proxy_Squid](../images/proxy_transparant_img/img1.png)

Avant de pouvoir activer Squid, j’ai défini la taille du cache sur le disque dans le champ **Hard Disk Cache Size**. Par défaut à 100 Mo, je l’ai passé à 1024 Mo (1 Go). J’ai ensuite cliqué sur **Save**.

![Proxy_Squid](../images/proxy_transparant_img/img2.png)

<br>

![Proxy_Squid](../images/proxy_transparant_img/img3.png)

#### Dans l’onglet General :
- J’ai activé **Enable Squid Proxy** pour démarrer le service.
- J’ai sélectionné **LAN** dans **Proxy Interface(s)**, car je souhaite que le proxy fonctionne uniquement sur ce réseau.

![Proxy_Squid](../images/proxy_transparant_img/img4.png)

- J’ai laissé le port proxy par défaut à **3128** (il ne sera pas utilisé manuellement grâce au mode transparent).
- J’ai coché **Allow Users on Interface** pour autoriser les utilisateurs du réseau LAN à utiliser le proxy.

![Proxy_Squid](../images/proxy_transparant_img/img5.png)

J’ai ensuite activé :
- **Transparent HTTP Proxy** (pour que le proxy fonctionne sans configuration côté client),
- **Enable SSL filtering** pour permettre le filtrage HTTPS (configuré plus tard),
- **Enable Access Logging** pour journaliser les connexions,
- **Rotate Logs** à 365 jours pour conserver un historique sur un an.

![Proxy_Squid](../images/proxy_transparant_img/img6.png)

<br>

![Proxy_Squid](../images/proxy_transparant_img/img7.png)

<br>

![Proxy_Squid](../images/proxy_transparant_img/img8.png)

Dans la section des personnalisations, j’ai défini :
- Un nom d’hôte visible,
- Un e-mail pour l’administrateur,
- La langue des messages d’erreur en français,
- Et j’ai masqué la version de Squid (**Suppress Squid Version**) pour des raisons de sécurité.

![Proxy_Squid](../images/proxy_transparant_img/img9.png)

Pour tester le bon fonctionnement de notre proxy transparent HTTP, je vais bloquer un nom de domaine.

Pour filtrer le HTTPS, j’ai commencé par créer une autorité de certification dans **System > Cert. Manager > CAs**. J’ai cliqué sur **Add**, donné un nom au certificat et validé.

![Proxy_Squid](../images/proxy_transparant_img/img10.png)

<br>

![Proxy_Squid](../images/proxy_transparant_img/img11.png)

<br>

![Proxy_Squid](../images/proxy_transparant_img/img12.png)

De retour dans Squid, j’ai :
- Activé **Resolve DNS IPv4 First** pour que les résolutions DNS se fassent avant le filtrage,
- Activé **Enable SSL Filtering**,
- Choisi **Splice All** dans **SSL/MITM Mode**, car ce mode est plus simple à déployer (pas besoin d’installer le certificat sur les clients),
- Sélectionné la CA que j’avais créée.

![Proxy_Squid](../images/proxy_transparant_img/img13.png)

<br>

![Proxy_Squid](../images/proxy_transparant_img/img14.png)

Dans l’onglet **ACLs**, j’ai ensuite testé le blocage d’un site en HTTPS via la section **Blacklist**, ce qui m’a permis de constater que le filtrage SSL était bien en place.

![Proxy_Squid](../images/proxy_transparant_img/img15.png)

<br>

![Proxy_Squid](../images/proxy_transparant_img/img16.png)

# SquidGuard (filtrage par catégories)

### Installation

J’ai installé **SquidGuard** via **Package Manager** comme pour Squid. Une fois installé, je suis allé dans **Services > SquidGuard Proxy Filter** pour le configurer.

Dans l’onglet **General Settings**, j’ai activé les logs (**Enable GUI Log** et **Enable log**), puis coché **Check this option to enable blacklist** pour activer la liste noire.

### Bibliothèque de règles

J’ai renseigné une **Blacklist URL** provenant de l’université Toulouse Capitole, car elle est en français et bien catégorisée (VPN, jeux, réseaux sociaux, etc.).

![Proxy_Squid](../images/proxy_transparant_img/img17.png)

Dans l’onglet **Blacklist**, j’ai cliqué sur **Download** pour récupérer la dernière version de la liste.

![Proxy_Squid](../images/proxy_transparant_img/img18.png)

Dans **Common ACL** :
- J’ai bloqué la catégorie VPN (**[blk_blacklists_vpn]**) en mettant **deny** dans la colonne **access**.

![Proxy_Squid](../images/proxy_transparant_img/img19.png)

- J’ai coché **Do not allow IP-Addresses in URL** pour empêcher le contournement via IP directe.
- J’ai activé **SafeSearch** pour les moteurs de recherche.
- J’ai aussi coché **Log** pour cette règle afin d’avoir une trace.

![Proxy_Squid](../images/proxy_transparant_img/img20.png)

Enfin, dans **General Settings**, j’ai activé SquidGuard (**Check this option to enable SquidGuard**) et cliqué sur **Apply**. Le statut est passé à **STARTED**, ce qui confirme le bon fonctionnement.

![Proxy_Squid](../images/proxy_transparant_img/img21.png)

## Résultats

Depuis un poste client, j’ai tenté d’accéder à un site lié à un VPN. L’accès a été bloqué, ce qui prouve que le filtrage par catégories fonctionne.

![Proxy_Squid](../images/proxy_transparant_img/img22.png)
