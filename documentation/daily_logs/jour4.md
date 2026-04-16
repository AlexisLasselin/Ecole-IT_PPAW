Daily Log - M1PPAW Projet 2 - Jour 4

Date : 16/04/2026
Durée : 7h
### Objectif du jour
Mettre en place :

 la protection applicative avec un WAF (ModSecurity)
 la détection réseau avec un IDS (Suricata)
 la centralisation des logs avec un SIEM (ELK)
 la visualisation et l’analyse des événements avec Kibana


### Travaux réalisés

####  1. Mise en place du WAF (ModSecurity)

 Fin du déploiement du WAF via Docker en frontal de l’application (Juice Shop)
 Configuration du reverse proxy (port 8080 → 3000)
 Activation des règles de sécurité personnalisées (XSS, SQLi)
 Vérification du bon fonctionnement via tests d’attaques 
 Analyse des logs dans error.log montrant les blocages (code 403)


####  2. Déploiement de l’IDS Suricata

 Installation de Suricata avec Docker
 Création de règles personnalisées (détection XSS / SQLi)
 Génération de trafic pour produire des événements réseau


####  3. Mise en place de la stack ELK

 Déploiement de Elasticsearch, Logstash et Kibana via Docker
 Configuration de Logstash pour ingérer :

     les logs WAF (access.log, error.log`)

 Création des index dans Elasticsearch :
    waf-logs-*


####  4. Visualisation dans Kibana

 Création des Data Views :
    waf-logs-*
Puis analyse des logs en temps réel dans Discover


### Difficultés rencontrées

#### 1. Absence de logs Suricata

#### 2. Problème de droits (read-only) :

* Erreur : Read-only file systeme sur suricata.yaml
* Cause : montage en lecture seule ( :ro )
* Solution : suppression du mode read-only dans le volume Docker



#### 3. Logs WAF non visibles dans ELK

* Problème : les attaques apparaissaient dans 'error.log' mais pas dans Kibana
* Cause : Logstash ne lisait pas ce fichier
* Solution : ajout de 'error.log' comme source dans logstash.conf



#### 4. Kibana inaccessible / index non détectés

* Problème : erreurs d’accès et absence d’index
* Cause : ingestion de logs incomplète
* Solution : vérification des pipelines Logstash et génération de trafic


### Résultat obtenu

* Détection d’attaques XSS et SQLi côté WAF
* Centralisation complète des logs dans Elasticsearch
* Visualisation fonctionnelle dans Kibana


### Conclusion

Cette journée a permis de mettre en place une architecture de sécurité combinant protection, détection et supervision. Malgré plusieurs difficultés techniques liées aux configurations réseau, nous avons réussi des solutions adaptées ont été trouvées pour assurer le bon fonctionnement global du système.

Le résultat final est une plateforme capable de détecter et visualiser des attaques en temps réel, illustrant concrètement les principes de sécurité applicative et réseau dans un environnement virtualisé.


