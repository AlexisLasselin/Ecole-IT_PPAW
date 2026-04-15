# Daily Log - M1PPAW Projet 2 - Jour 2

Date : 14/04/2026
Durée : 7h
Objectif : Reconnaissance + premières vulnérabilités

1. Reconnaissance

   L'équipe s'est répartie entre deux cibles : Juice Shop et crAPI. Nous avons utilisé les outils suivants pour la reconnaissance :
   - Nmap : pour scanner les ports ouverts
   - Gobuster : pour découvrir les répertoires cachés
   - Burp Suite : pour intercepter les requêtes HTTP

   Résultats :
   - Juice Shop : ports 3000 (HTTP) ouverts, plusieurs répertoires découverts (/admin, /api)
   - crAPI : ports 80 (HTTP) ouverts, répertoires découverts (/api, /docs)

2. Premières Vulnérabilités

   Comme vous le verrez dans [le rapport des vulnérabilités](../reports/vulnerabilities.md), nous avons identifié plusieurs vulnérabilités sur les deux applications. Voici un aperçu des principales découvertes :
   - Juice Shop semble vulnérable à une injection SQL dans le champ de login, permettant un accès non autorisé au compte administrateur, ainsi qu'à une attaque de brute force sur le même champ. Mais également d'autres vulnérabilités comme des failles XSS, CSRF, etc.
   - crAPI présente des vulnérabilités d'exposition d'informations sensibles via les endpoints API, ainsi que des failles d'authentification faibles.

   En revanche, certaines sécurités étaient tout de même en place, un "anticheat" semble empêcher l'accès aux /etc/passwd, et les mots de passe sont hashés dans la base de données. Cependant, cela n'empêche pas l'exploitation des vulnérabilités identifiées.

3. Début d'automatisation

   Nous avons commencé à automatiser certaines étapes de reconnaissance et d'exploitation des vulnérabilités à l'aide de scripts Python et d'outils comme SQLMap pour les injections SQL. Cela nous permettra de gagner du temps lors de l'exploitation des vulnérabilités et de nous concentrer sur l'analyse des résultats.

4. Chaining

   Nous avons également commencé à réfléchir à la manière de chaîner les différentes vulnérabilités pour maximiser l'impact de nos attaques. Par exemple, en utilisant un XSS pour récupérer les cookies administrateur, puis en exploitant une injection SQL pour accéder à la base de données et exfiltrer des données sensibles. Nous continuerons à explorer ces possibilités dans les prochains jours.

5. Prochaines étapes
   - Continuer la reconnaissance pour identifier d'autres vulnérabilités potentielles
   - Exploiter les vulnérabilités identifiées pour comprendre leur impact et les risques associés
   - Documenter toutes les étapes de reconnaissance et d'exploitation dans le daily log et le rapport des vulnérabilités
   - Commencer à travailler sur les recommandations de sécurité pour chaque vulnérabilité identifiée
