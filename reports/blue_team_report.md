# Rapport Blue Team : Sécurisation Juice Shop

Suite aux phases Red Team réalisées sur l’application OWASP Juice Shop, plusieurs vulnérabilités exploitables ont été identifiées, notamment des injections SQL, des attaques XSS et des accès non sécurisés à certaines ressources. Afin de réduire la surface d’attaque, une stratégie de défense en profondeur a été mise en place avec un WAF basé sur ModSecurity ainsi que des règles personnalisées.

Ces mesures ont permis de bloquer efficacement les attaques testées, avec une transition d’un état vulnérable où les requêtes atteignaient l’application vers un état sécurisé où elles sont interceptées en amont avec un code HTTP 403.

## Vulnérabilités Corrigées

### Critique

1. **SQL Injection sur /rest/products/search**  
   Initialement, une requête contenant `' OR 1=1--` provoquait une erreur SQL côté application (500 SQLITE_ERROR), prouvant que l’injection atteignait la base de données.

2. **XSS Reflected via paramètre q**  
   L’injection de payloads HTML/JavaScript dans les paramètres d’URL était acceptée et traitée par l’application.

3. **Accès non restreint à certaines ressources (File Access)**  
   Plusieurs fichiers accessibles via `/ftp` exposaient des informations internes sans authentification.

4. **Brute Force sur le champ de login**  
   Aucune protection n’était en place pour limiter les tentatives de connexion, permettant des attaques de force brute.

## Défenses Mises en Place

### Couche 4 : Fail2ban

Un système de prévention d’intrusion basé sur Fail2ban a été configuré pour surveiller les entrées et bloquer les adresses IP après un certain nombre de tentatives d’attaque détectées dans les logs du WAF, cela nous permet de limiter les attaques de brute force et de réduire la surface d’attaque en bloquant les sources d’attaques répétées.

Voici les règles mises en place :

- Nombre de tentatives : 5
- Durée du bannissement : 10 heures
- Code d'accès déclencheur : 4XX (toutes les requêtes pouvant être considérées comme malveillantes)
- Action : iptables-multiport pour bloquer les adresses IP sur les ports 80 et 3000
- Log : les événements de sécurité sont extraits des logs ModSecurity pour identifier les tentatives d’attaque.

### Couche 7 : WAF (ModSecurity + Nginx)

Un Web Application Firewall basé sur ModSecurity a été déployé devant l’application Juice Shop via un conteneur Docker agissant comme reverse proxy.

Des règles personnalisées ont été ajoutées afin de détecter et bloquer les attaques principales observées lors de la phase Red Team :

- Détection des patterns XSS (balises script, attributs onerror, onload)
- Détection des injections SQL (union select, or 1=1, select from)
- Détection des tentatives de path traversal

Le moteur ModSecurity a été activé en mode blocage avec `SecRuleEngine On`, permettant de refuser les requêtes malveillantes avec un code HTTP 403.

Les tests ont confirmé que les requêtes contenant des payloads SQLi et XSS sont désormais bloquées avant d’atteindre l’application.

**Métriques :**

- 100% des attaques SQLi testées bloquées
- 100% des attaques XSS testées bloquées
- 0% d’accès à l’application pour les requêtes malveillantes
- Réduction complète des erreurs applicatives liées aux injections (plus de 500 SQL)

### Logging et Analyse des Événements

Les logs générés par ModSecurity ont permis d’analyser précisément les tentatives d’attaque.

Chaque événement contient :

- l’adresse IP du client
- l’URL ciblée
- le payload injecté
- l’identifiant de la règle déclenchée
- le message associé à la détection

Les identifiants de règles jouent un rôle central dans l’analyse. Par exemple, l’ID 1002 correspond à la règle de détection des injections SQL et permet d’identifier rapidement la nature de l’attaque. L’ID 1001 correspond quant à lui à la détection des attaques XSS.

Grâce à ces informations, il est possible de retracer les actions réalisées par un attaquant, comprendre quelles techniques ont été utilisées et adapter les règles si nécessaire.

Les logs permettent également de distinguer les erreurs applicatives des événements de sécurité, comme dans le cas des erreurs de connexion entre le WAF et l’application.

**Métriques :**

- 100% des attaques bloquées sont loguées
- Identification claire du type d’attaque via les IDs de règles
- Possibilité de corrélation des événements via les identifiants uniques

### Isolation et Sécurisation de l’Environnement

L’application Juice Shop a été placée derrière un WAF dans une architecture en reverse proxy.

Le trafic utilisateur passe désormais obligatoirement par le WAF avant d’atteindre l’application.

Cela permet de filtrer les requêtes en amont et d’empêcher toute exploitation directe des vulnérabilités.

L’utilisation de Docker permet également d’isoler les composants et de faciliter le déploiement et la gestion de la sécurité.

## Tests de Validation (Purple Team)

Des tests ont été réalisés après la mise en place des défenses afin de valider leur efficacité.

Les mêmes attaques que celles utilisées en phase Red Team ont été rejouées :

- Injection SQL via `' OR 1=1--`
- Injection XSS via `<img src=x onerror=alert(1)>`
- Accès direct à des fichiers sensibles via `/ftp/package.json`
- Tentatives de brute force sur le champ de login

Avant la mise en place du WAF, ces attaques provoquaient des erreurs applicatives ou étaient acceptées par le système.

Après déploiement du WAF, ces mêmes requêtes sont systématiquement bloquées avec un code HTTP 403.

Les logs ModSecurity confirment que les règles personnalisées sont bien déclenchées et que les attaques sont interceptées en phase 2 du traitement HTTP.

## Conclusion

La mise en place de nombreuses mesures de défense a permis de sécuriser efficacement l’application Juice Shop contre les attaques identifiées lors de la phase Red Team. Le WAF basé sur ModSecurity a joué un rôle central en bloquant les requêtes malveillantes avant qu’elles n’atteignent l’application, tandis que Fail2ban a permis de limiter les attaques répétées. Les tests de validation ont confirmé l’efficacité de ces mesures, avec une réduction complète des attaques exploitant les vulnérabilités précédemment identifiées.

Les logs générés par le WAF fournissent une visibilité précieuse sur les tentatives d’attaque et permettent d’adapter les règles de sécurité en fonction des nouvelles menaces. L’architecture en reverse proxy assure une isolation efficace de l’application, renforçant ainsi la sécurité globale du système.

## Prochaines Étapes

- Continuer à surveiller les logs pour détecter de nouvelles tentatives d’attaque
- Adapter les règles du WAF en fonction des nouvelles techniques d’attaque observées
- Envisager l’ajout de mesures de sécurité supplémentaires, comme une authentification renforcée ou des mécanismes de détection d’intrusion plus avancés
