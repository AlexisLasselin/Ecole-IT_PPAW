# Daily Log - M1PPAW Projet 2 - Jour 3

Date : 15/04/2026  
Durée : 7h  
Objectif du jour : Mise en place d’une défense active avec un WAF, correction des erreurs de configuration, validation du blocage des attaques identifiées lors de la phase Red Team

---

Mise en place du WAF (ModSecurity + Nginx)

Lors de cette journée, nous avons commencé par mettre en place un Web Application Firewall afin de protéger l’application OWASP Juice Shop précédemment exploitée. L’objectif était de placer un composant intermédiaire capable d’analyser les requêtes HTTP et de bloquer les attaques avant qu’elles n’atteignent l’application.

Nous avons créé un environnement dédié avec Docker Compose afin d’orchestrer deux services, à savoir l’application Juice Shop et un conteneur WAF basé sur ModSecurity avec Nginx.

Commandes utilisées pour préparer l’environnement :

mkdir ~/projet_m1ppaw/waf
cd ~/projet_m1ppaw/waf
nano docker-compose.yml

Configuration initiale du fichier docker-compose.yml avec deux services, un pour l’application et un pour le WAF.

---

Problèmes rencontrés lors du déploiement

Lors du premier lancement du WAF, plusieurs erreurs critiques ont été rencontrées, notamment liées à Docker et à la configuration des volumes.

Commande utilisée :

docker-compose up -d

Erreur rencontrée indiquant un problème de montage de fichier entre l’hôte et le conteneur. Cette erreur provenait du fait que le fichier de configuration Nginx n’existait pas encore sur la machine hôte.

Nous avons corrigé cela en créant manuellement les fichiers nécessaires.

mkdir -p conf
cd conf
nano default.conf.template
nano modsecurity-override.conf.template

Une autre erreur importante a été identifiée dans le fichier docker-compose.yml, où un caractère incorrect cassait la syntaxe YAML et empêchait Docker de reconnaître correctement les volumes.

Correction effectuée en réécrivant proprement la section volumes.

---

Problème de structure YAML

Une erreur supplémentaire a été rencontrée lors de l’exécution de docker-compose indiquant que le service waf n’était pas reconnu.

Commande utilisée :

docker-compose up -d

Erreur indiquant que la propriété waf n’était pas autorisée. Après analyse, nous avons identifié que le service était déclaré en dehors de la section services, ce qui rendait le fichier invalide.

Correction réalisée en restructurant entièrement le fichier docker-compose.yml.

Commande de validation :

docker-compose config

---

Activation du WAF

Une fois le conteneur correctement démarré, nous avons constaté que les attaques passaient toujours et provoquaient des erreurs côté application, notamment une erreur SQLITE_ERROR indiquant que l’injection SQL atteignait toujours la base de données.

Cela nous a permis de comprendre que le WAF était actif mais en mode détection uniquement.

Nous avons alors modifié le fichier de configuration ModSecurity pour activer le mode blocage.

nano ~/projet_m1ppaw/waf/conf/modsecurity-override.conf.template

Ajout de la directive :

SecRuleEngine On

---

Ajout de règles personnalisées

Afin de renforcer la protection, nous avons implémenté des règles personnalisées pour détecter les attaques identifiées lors de la phase Red Team.

Ces règles permettent de détecter des patterns spécifiques liés aux injections SQL, aux attaques XSS et aux tentatives de path traversal.

Après modification du fichier de configuration, nous avons redémarré les conteneurs.

docker-compose down
docker-compose up -d

---

Validation du fonctionnement du WAF

Une fois la configuration appliquée, nous avons testé les attaques précédemment identifiées.

Test d’injection SQL :

http://localhost:8080/rest/products/search?q=' OR 1=1--

Test XSS :

http://localhost:8080/?q=<img src=x onerror=alert(1)>

Avant la mise en place du WAF, ces requêtes provoquaient des erreurs ou étaient acceptées par l’application.

Après configuration du WAF, les requêtes sont bloquées avec un code HTTP 403, ce qui confirme que le filtrage est actif.

---

Analyse des logs

Afin de confirmer le comportement du WAF, nous avons analysé les logs générés par le conteneur.

Commande utilisée :

sudo docker exec -it modsecurity-waf /bin/bash
cat /var/log/nginx/error.log

Les logs montrent clairement que les requêtes malveillantes sont détectées et bloquées, avec des messages indiquant la règle déclenchée et l’identifiant associé.

Ces informations permettent de comprendre précisément quelle attaque a été détectée et pourquoi elle a été bloquée.

---

Problèmes techniques rencontrés

Plusieurs problèmes ont été rencontrés au cours de cette phase, notamment des erreurs de configuration Docker, des problèmes de syntaxe YAML, des fichiers manquants et des règles ModSecurity initialement mal écrites.

Ces problèmes ont nécessité plusieurs itérations de correction, notamment la recréation de fichiers de configuration, la correction des chemins de montage et la simplification temporaire du docker-compose pour isoler les erreurs.

Nous avons également constaté que certaines attaques continuaient à passer tant que le WAF n’était pas correctement configuré en mode blocage, ce qui a permis de mieux comprendre le fonctionnement interne de ModSecurity.