# Daily Log - M1PPAW Projet 2 - Jour 2

Date : 14/04/2026  
Durée : 7h  
Objectif : Reconnaissance + premières vulnérabilités

---

## 1. Reconnaissance

L'équipe s'est répartie entre deux cibles : Juice Shop et crAPI. Nous avons utilisé les outils suivants pour la reconnaissance :

- Nmap : pour scanner les ports ouverts
- Gobuster : pour découvrir les répertoires cachés
- Burp Suite : pour intercepter les requêtes HTTP

Nous avons également utilisé une approche manuelle en naviguant dans les applications afin d’identifier les fonctionnalités principales (login, recherche, panier, API). L’analyse des requêtes interceptées dans Burp Suite nous a permis d’identifier plusieurs endpoints importants comme `/rest/user/login`, `/rest/products/search` ou encore `/rest/user/whoami`.

Résultats :

- Juice Shop : ports 3000 (HTTP) ouverts, plusieurs répertoires découverts (/admin, /api, /ftp)
- crAPI : ports 80 (HTTP) ouverts, répertoires découverts (/api, /docs)

Nous avons également observé que certaines routes ne sont pas directement visibles dans l’interface mais peuvent être découvertes via des outils comme Gobuster ou en analysant les requêtes réseau.

---

## 2. Premières Vulnérabilités

Comme vous le verrez dans [le rapport des vulnérabilités](../reports/vulnerabilities.md), nous avons identifié plusieurs vulnérabilités sur les deux applications. Voici un aperçu des principales découvertes :

- Juice Shop semble vulnérable à une injection SQL dans le champ de login, permettant un accès non autorisé au compte administrateur, ainsi qu'à une attaque de brute force sur le même champ. Mais également d'autres vulnérabilités comme des failles XSS, CSRF, etc.
- crAPI présente des vulnérabilités d'exposition d'informations sensibles via les endpoints API, ainsi que des failles d'authentification faibles.

Nous avons également identifié une vulnérabilité de type **File Access / Security Misconfiguration** sur Juice Shop, permettant l’accès à des fichiers sensibles via le répertoire `/ftp` (ex : `package.json`, `acquisitions.md`). Certains de ces fichiers contiennent des informations internes ou confidentielles.

Concernant les XSS, nous avons observé que certaines injections ne fonctionnent pas directement sur les endpoints API car les réponses sont en JSON. En revanche, les vulnérabilités XSS sont exploitables dans les parties de l’application qui rendent du HTML (ex : barre de recherche ou commentaires produits).

En revanche, certaines sécurités étaient tout de même en place, un "anticheat" semble empêcher l'accès aux /etc/passwd, et les mots de passe sont hashés dans la base de données. Cependant, cela n'empêche pas l'exploitation des vulnérabilités identifiées. Nous avons notamment observé des réponses HTTP 403 indiquant la présence de mécanismes de protection côté serveur.

---

## 3. Début d'automatisation

Nous avons commencé à automatiser certaines étapes de reconnaissance et d’exploitation des vulnérabilités à l’aide de scripts Python. Ces scripts permettent notamment de tester des injections SQL sur le système d’authentification, de réaliser des tentatives de brute force sur les comptes utilisateurs, ainsi que de détecter l’exposition de fichiers sensibles via le répertoire /ftp. Cela nous permet de gagner du temps lors de l’exploitation des vulnérabilités et de nous concentrer davantage sur l’analyse des résultats.

Les scripts développés restent simples et peu structurés, mais ils permettent déjà d’automatiser des tâches répétitives. Nous avons également rencontré quelques erreurs dans les scripts (ex : concaténation incorrecte de noms de fichiers), ce qui nous a permis d’identifier des problèmes classiques et de les corriger.

---

## 4. Chaining

Nous avons également commencé à réfléchir à la manière de chaîner les différentes vulnérabilités pour maximiser l'impact de nos attaques. Par exemple, en utilisant un XSS pour récupérer les cookies administrateur, puis en exploitant une injection SQL pour accéder à la base de données et exfiltrer des données sensibles. Nous continuerons à explorer ces possibilités dans les prochains jours.

Nous avons aussi envisagé des scénarios combinant plusieurs vulnérabilités, comme l’utilisation d’un accès aux fichiers exposés pour récupérer des informations internes, puis exploiter ces informations pour faciliter une attaque (authentification, endpoints cachés, etc.).

---

## 5. Prochaines étapes

- Continuer la reconnaissance pour identifier d'autres vulnérabilités potentielles
- Exploiter les vulnérabilités identifiées pour comprendre leur impact et les risques associés
- Documenter toutes les étapes de reconnaissance et d'exploitation dans le daily log et le rapport des vulnérabilités
- Commencer à travailler sur les recommandations de sécurité pour chaque vulnérabilité identifiée
