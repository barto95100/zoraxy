
# zoraxy
![Getting Started](https://github.com/tobychui/zoraxy/raw/main/img/title.png)

## Description
Zoraxy est ce que l'ont nome un reverse proxy.

Un reverse proxy est conçue pour améliorer les performances, la sécurité et la disponibilité des services web. Un reverse proxy agit comme une interface entre les utilisateurs et les serveurs backend, en transmettant les requêtes aux serveurs appropriés en fonction de règles de routage configurées.

Un des outils le plus connu dans ce domaine serais NPM (Nginx Propxy Manager)

Cette installation docker inclus certbot pour la génération des certificats wildcard avec les API OVH, qui n'existe pas dans l'image docker officiel

Il inclus tous le necessaires pour pouvoir généré les certificats grace au DNS-challenge OVH

## Fonctionnalités - Caractéristiques

* Interface simple à utiliser avec des instructions détaillées dans le système

* Proxy inverse

    * Répertoire virtuel
    * Authentification de base
    * En-têtes personnalisés
* Règles de redirection

* Configuration et déploiement TLS/SSL

    * Fonctionnalités ACME telles que le renouvellement automatique pour servir vos sites en https
    * Prise en charge SNI (un certificat contient plusieurs noms d'hôte)
* Liste noire / Liste blanche par pays ou adresse IP (IP unique, CIDR ou wildcard pour les débutants)

* Interface utilisateur Web du contrôleur de réseau mondial (ZeroTier non inclus)

* Tunneling TCP / Proxy

* Moniteur de disponibilité intégré (Up-time)

* Terminal Web-SSH

* Utilitaires

    * Convertisseurs IP CIDR
    * Scanner mDNS
    * Scanner IP
* Autres

    * Mode de gestion de base à administrateur unique
    * Système de gestion des autorisations externe pour une intégration facile du système
    * Configuration SMTP pour la réinitialisation du mot de passe

## Pre-requis

Vous devez être en possesion de :
- Un nom de domaine chez OVH
- Clef API OVH (explication en dessous)
- Docker et docker compose d'installer sur votre machine

## API OVH

Vous devez aller sur l'adresse suivante : [OVH API](https://www.ovh.com/auth/api/createToken)

Renseigner les informations comme ci-dessous (ne pas oublier d'indiquer votre nom de domaine en lieu et place de *VOTRE_NOM_DE_DOMAINE*)

**Nom d'application:** `Zoraxy`

**Application Description:** `Reverse proxy`

**GET** `/domain/zone/`

**GET** `/domain/zone/VOTRE_NOM_DE_DOMAINE/*`

**PUT** `/domain/zone/VOTRE_NOM_DE_DOMAINE/*`

**POST** `/domain/zone/VOTRE_NOM_DE_DOMAINE/*`

**DELETE** `/domain/zone/VOTRE_NOM_DE_DOMAINE/*`

Récupéré les informations : (*Ces informations sont a metre dans le docker compose ci-dessous*)

`application_key`

`application_secret`

`consumer_key`


## Docker Compose
```
version: '3'
services:
  zoraxy-docker:
    image: ghcr.io/barto95100/zoraxy:latest
    container_name: zoraxy
    volumes:
      - /docker/zoraxy/conf:/opt/zoraxy/src/conf
    ports:
      - 80:80
      - 443:443
      - 8000:8000/tcp
    environment:
      - DOMAIN_NAME=xxx.yyyyyy.zzzz
      - EMAIL_LETSENCRYPT=exemple@mail.com
      - OVH_ENDPOINT=ovh-eu
      - OVH_APPLICATION_KEY=MdAdMdAdMdAdMdAdMdAd
      - OVH_APPLICATION_SECRET=MdAdMdAdMdAdMdAdMdAdMdAdMdAdMdAdMdAdMdAd
      - OVH_CONSUMER_KEY=MdAdMdAdMdAdMdAdMdAdMdAdMdAdMdAd
      - TZ=Europe/Paris
```
Remplacer `www.yyyyyy.zzzz`par votre nom de domaine

Remplacer `exemple@mail.com` par votre adresse mail (*cette adresse email sera utiliser pour vous envoyer des notifications quand le certificat arrivera a expiration, certbot regenère automatiquement les certificats avec une durée de validité de moins de 30 jours, mais si cela n'est pas fait, il vaut mieu etre avertie pour menez les actions necessaires*)

Remplacer les lignes `MdAdMdAdMdAdMdAdMdAdMdAd` par les valeurs que vous avez récupéré suite à la génération du TOKEN sur [API OVH](#api-ovh)

      - OVH_APPLICATION_KEY=MdAdMdAdMdAdMdAdMdAd
      - OVH_APPLICATION_SECRET=MdAdMdAdMdAdMdAdMdAdMdAdMdAdMdAdMdAdMdAd
      - OVH_CONSUMER_KEY=MdAdMdAdMdAdMdAdMdAdMdAdMdAdMdAd

## Démarrage
Excuter la commande : 
```bash
docker compose up -d
```

Verifier le bon fonctionnement du container, pour cela faite :
```bash
docker logs -f zoraxy
```

Vous devriez voir des informations qui permettent de valider la bonne prise en compte de vos information d'API OVH, ainsi que les logs de certbot sur la generation des certificats et pour fini le lancement de Zoraxy :
```
[+] Running 1/0
 ✔ Container zoraxy  Created                                                                                      0.0s
Attaching to zoraxy
zoraxy  | Contenu du fichier .ovhapi:

zoraxy  | dns_ovh_endpoint = ovh-eu
zoraxy  | dns_ovh_application_key = xxxxxxxxxxxxxxxxxxxx
zoraxy  | dns_ovh_application_secret = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
zoraxy  | dns_ovh_consumer_key = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

zoraxy  | Génération du certificat let's 

zoraxy  | Saving debug log to /var/log/letsencrypt/letsencrypt.log
zoraxy  | Requesting a certificate for xxxxxxxx.fr and *.xxxxxxxx.fr
zoraxy  | Waiting 30 seconds for DNS changes to propagate
zoraxy  |
zoraxy  | Successfully received certificate.
zoraxy  | Certificate is saved at: /etc/letsencrypt/live/xxxxxxxx.fr/fullchain.pem
zoraxy  | Key is saved at:         /etc/letsencrypt/live/xxxxxxxx.fr/privkey.pem
zoraxy  | This certificate expires on 2024-06-06.
zoraxy  | These files will be updated when the certificate renews.
zoraxy  | Certbot has set up a scheduled task to automatically renew this certificate in the background.
zoraxy  |
zoraxy  | - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
zoraxy  | If you like Certbot, please consider supporting our work by:
zoraxy  |  * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
zoraxy  |  * Donating to EFF:                    https://eff.org/donate-le
zoraxy  | - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -

zoraxy. | Démarrage de Zoraxy

zoraxy  | 2024/03/08 18:25:24 [Auth] Authentication session key loaded from database
zoraxy  | 2024/03/08 18:25:26 Static Web Server started. Listeing on :5487
zoraxy  | 2024/03/08 18:25:26 Environment variable ZT_AUTH not defined. Trying to load authtoken from file.
zoraxy  | 2024/03/08 18:25:26 Unable to read authkey at /var/lib/zerotier-one/authtoken.secret:  exec: "sudo": executable file not found in $PATH
zoraxy  | 2024/03/08 18:25:26 [INFO] Failed to load ZeroTier controller API authtoken
zoraxy  | 2024/03/08 18:25:26 [INFO] Starting ACME handler
zoraxy  | 2024/03/08 18:25:26 [INFO] Inbound port not set. Using default (80)
zoraxy  | 2024/03/08 18:25:26 [INFO] TLS mode disabled. Serving proxy request with plain http
zoraxy  | 2024/03/08 18:25:26 [INFO] Force latest TLS mode disabled. Minimum TLS version is set to v1.0
zoraxy  | 2024/03/08 18:25:26 [INFO] Development mode disabled. Proxying with default Cache Control policy
zoraxy  | 2024/03/08 18:25:26 [INFO] Port 80 listener disabled
zoraxy  | 2024/03/08 18:25:26 [INFO] Force HTTPS mode disabled
zoraxy  | 2024/03/08 18:25:26 Reverse proxy service started in the background (Plain HTTP mode)
zoraxy  | 2024/03/08 18:25:26 [INFO] Dynamic Reverse Proxy service started
zoraxy  | 2024/03/08 18:25:26 [INFO] Uptime Monitor background service started
zoraxy  | 2024/03/08 18:25:26 [INFO] Assigned temporary port:64131
zoraxy  | 2024/03/08 18:25:26 [INFO] Zoraxy started. Visit control panel at http://localhost:8000
```

Vous n'avez plus qu'a vous connecter sur le port `8000`de votre machine docker et commencer a configuré Zoraxy

Si jamais vous ne voulez pas généré de certificat wildcard : `*.xxxxxx.fr`

Suivez les instructions sur le git Officiel[ ZORAXY](https://github.com/tobychui/zoraxy/tree/main/docker), vous pourrez généré des certificats pour chaque FQDN creer dans zoraxy.

## Images
Accueil
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/d7ef50d6-c944-4395-8759-55c3560b53c8)

État
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/28cfcb63-978f-4cb0-a902-594d8f6aeabc)

Statistique
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/549e174a-0749-4c06-94aa-74680bc0f44c)

Site par default
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/7b2cb01c-d412-4053-88a0-2b78a6557ef2)


Proxy HTTP
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/edcce534-cc16-434d-88ce-c382af24e9bc)

Répertoire virtuel (uri)
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/7e42a875-2084-4022-8fab-a012f16f3590)

Proxy TCP
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/998f18c9-d77b-439d-bcd2-cb896c28b953)


Outils réseau - obtenir des informations sur votre réseau ou réveiller des appareils via WOL (Wake On Lan)
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/7d8076bf-be82-49c2-afa2-fcd436a05b5f)



Web-SSH - Si vous désirez vous connectez en ssh sur vos équipements interne de votre reseau
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/aab2b6eb-9330-4412-b017-f6549f5d4cbf)



TLS / SSL - Téléverser vos certificats si vous en possédé 
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/8a90e1f7-e878-42a1-9014-ae470716fad4)


Redirection
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/c9c15fbf-1865-4907-bb2e-91773119c4f1)


Contrôleur de réseau (Basé sur Zérotier)
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/9d45ee6d-9f4d-420e-a261-c67dccb7a056)
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/0b9f06f7-8794-4e73-b910-513d7b331f7b)


Uptime Monitor - vérifier si vos services sont disponibles
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/13949eca-49fa-4109-9494-96380f82919f)


Utilitaire - Modification mot de passe, configuration SMTP (envoie de mail) etc...
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/3474b994-3d42-498e-b941-98593c535480)


Statistiques
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/6f004a85-8e5f-4181-97d9-d3c9c773c4f1)



Encore plus de statistiques
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/4ef8cab6-397d-456c-9cb4-18cb58b227b1)


Et analyse des visiteurs et des tendances
![圖片](https://github.com/tobychui/zoraxy/assets/24617523/ebf930dd-95e2-41e1-ae92-95e672f9ae7a)

## Licence
Ce projet est open source sous licence AGPL. 
Ce projet est rendu open source afin que tout le monde puisse vérifier les problèmes de sécurité et bénéficier à tous les utilisateurs. 

Si vous envisagez d'utiliser ce projet dans un environnement commercial (qui viole les conditions AGPL), veuillez contacter toby@imuslab.com pour une licence alternative.
