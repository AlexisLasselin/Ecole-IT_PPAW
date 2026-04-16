# Rapport Blue Team : Sécurisation Juice Shop


Suite aux phases Red Team réalisées sur l’application OWASP Juice Shop, plusieurs vulnérabilités exploitables ont été identifiées, notamment des injections SQL, des attaques XSS et des accès non sécurisés à certaines ressources. Afin de réduire la surface d’attaque, une stratégie de défense en profondeur a été mise en place avec un WAF basé sur ModSecurity ainsi que des règles personnalisées.

Ces mesures ont permis de bloquer efficacement les attaques testées, avec une transition d’un état vulnérable où les requêtes atteignaient l’application vers un état sécurisé où elles sont interceptées en amont avec un code HTTP 403.

---

## Vulnérabilités Corrigées

### Critique

1. **SQL Injection sur /rest/products/search**  
   Initialement, une requête contenant `' OR 1=1--` provoquait une erreur SQL côté application (500 SQLITE_ERROR), prouvant que l’injection atteignait la base de données.

2. **XSS Reflected via paramètre q**  
   L’injection de payloads HTML/JavaScript dans les paramètres d’URL était acceptée et traitée par l’application.

3. **Accès non restreint à certaines ressources (File Access)**  
   Plusieurs fichiers accessibles via `/ftp` exposaient des informations internes sans authentification.

---

## Défenses Mises en Place

### Couche 1 : WAF (ModSecurity + Nginx)

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

---

### Couche 2 : Logging et Analyse des Événements

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

---

### Couche 3 : Isolation et Architecture Sécurisée

L’application Juice Shop a été placée derrière un WAF dans une architecture en reverse proxy.

Le trafic utilisateur passe désormais obligatoirement par le WAF avant d’atteindre l’application.

Cela permet de filtrer les requêtes en amont et d’empêcher toute exploitation directe des vulnérabilités.

L’utilisation de Docker permet également d’isoler les composants et de faciliter le déploiement et la gestion de la sécurité.

---

## Tests de Validation (Purple Team)

Des tests ont été réalisés après la mise en place des défenses afin de valider leur efficacité.

Les mêmes attaques que celles utilisées en phase Red Team ont été rejouées :

- Injection SQL via `' OR 1=1--`
- Injection XSS via `<img src=x onerror=alert(1)>`

Avant la mise en place du WAF, ces attaques provoquaient des erreurs applicatives ou étaient acceptées par le système.

Après déploiement du WAF, ces mêmes requêtes sont systématiquement bloquées avec un code HTTP 403.

Les logs ModSecurity confirment que les règles personnalisées sont bien déclenchées et que les attaques sont interceptées en phase 2 du traitement HTTP.

---

## Conclusion

La mise en place du WAF ModSecurity avec des règles personnalisées a permis de transformer une application vulnérable en un système capable de bloquer activement les attaques.

Les résultats montrent une amélioration significative de la sécurité, avec un blocage complet des attaques testées et une visibilité accrue grâce aux logs détaillés.

Cette approche démontre l’efficacité d’une stratégie combinant analyse offensive et mise en place de mécanismes défensifs, dans une logique Purple Team.