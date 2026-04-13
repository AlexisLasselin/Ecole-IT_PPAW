# Plan d'Attaque - Projet 2 (Red Team vs Blue Team)

## 1. Executive Summary

Dans ce projet, l’objectif est d’analyser plusieurs applications web vulnérables (OWASP Juice Shop, crAPI et VAmPI) afin d’identifier des failles de sécurité, les exploiter et comprendre leur impact.

Pour cela, on va suivre une méthodologie inspirée de la Cyber Kill Chain adaptée au pentest web : reconnaissance, scanning, exploitation, post-exploitation puis mise en place de défenses.

Les livrables attendus sont :
- Un rapport technique détaillant les vulnérabilités
- Un résumé exécutif plus simple
- Des preuves d’exploitation (captures, requêtes, etc.)
- Des recommandations pour corriger les failles

---

## 2. Scope et Périmètre

### Assets analysés
- OWASP Juice Shop (application web)
- OWASP crAPI (API en microservices)
- VAmPI (API REST vulnérable)

---

### IN SCOPE
- Tests des vulnérabilités OWASP Top 10 (XSS, SQLi, CSRF, IDOR…)
- Analyse des systèmes d’authentification
- Tests sur les API (validation des entrées, auth, rate limiting)
- Exploitation des failles trouvées

---

### OUT OF SCOPE
- Attaques de type DoS / DDoS
- Attaques sur la machine hôte ou VMware
- Bruteforce intensif
- Tests en dehors de l’environnement local

---

### Contraintes
- Temps limité à 5 jours
- Travail uniquement en local
- Ressources VM limitées (RAM, CPU)
- Utilisation d’outils open-source

---

## 3. Méthodologie Détaillée

---

### Phase 1 : Reconnaissance (Jour 1 - 3h)

**Objectifs :**
- Identifier les différentes pages, endpoints et fonctionnalités
- Comprendre comment fonctionnent les applications
- Repérer les zones potentiellement vulnérables

**Outils :**
- WhatWeb
- Gobuster
- Navigateur (analyse manuelle)

**Critères de succès :**
- Liste des endpoints trouvés
- Technologies utilisées identifiées
- Vue globale de l’application

**Temps estimé :** 3h

**Risques :**
- Certaines routes peuvent être cachées : utiliser de meilleures wordlists

---

### Phase 2 : Scanning (Jour 1-2 - 3h)

**Objectifs :**
- Détecter des vulnérabilités automatiquement
- Avoir une première vision des problèmes de sécurité

**Outils :**
- Nikto
- Burp Suite

**Critères de succès :**
- Rapport Nikto généré
- Points d’entrée identifiés

**Temps estimé :** 3h

**Risques :**
- Faux positifs à vérifier manuellement

---

### Phase 3 : Exploitation (Jour 2-3 - 6h)

**Objectifs :**
- Exploiter les vulnérabilités identifiées
- Accéder à des données sensibles

**Outils :**
- Burp Suite
- SQLMap

**Tests réalisés :**
- SQL Injection (bypass login, extraction données)
- XSS (stocké et réfléchi)
- IDOR (accès à d’autres comptes)
- CSRF (actions sans autorisation)

**Critères de succès :**
- Au moins 1 SQLi fonctionnelle
- 2 XSS exploitables
- 1 IDOR validé
- 1 CSRF réussi

**Temps estimé :** 6h

**Risques :**
- Protection côté serveur : contourner avec encodage
- Auth complexe : analyser tokens/session

---

### Phase 4 : Post-exploitation (Jour 3-4 - 3h)

**Objectifs :**
- Évaluer l’impact réel des attaques
- Combiner plusieurs vulnérabilités

**Actions :**
- Escalade de privilèges
- Accès à d’autres comptes
- Extraction de données

**Critères de succès :**
- Accès à un compte admin
- Accès à des données sensibles

**Temps estimé :** 3h

**Risques :**
- Vulnérabilités difficiles à chaîner

---

### Phase 5 : Défense (Blue Team) (Jour 4 - 4h)

**Objectifs :**
- Voir si les attaques sont détectables
- Proposer des solutions de sécurité

**Outils :**
- Kibana
- Suricata

**Critères de succès :**
- Détection d’au moins 2 attaques dans les logs
- Propositions de correction

**Temps estimé :** 4h

**Risques :**
- Peu de logs exploitables : utiliser tcpdump

---

## 4. Planning Prévisionnel

Le planning du projet est réparti sur cinq jours. 
- Le premier jour (J1) est consacré au setup de l’environnement ainsi qu’à la phase de reconnaissance, avec comme activités principales l’installation des outils et la découverte des applications, ce qui permet de produire des premières notes et de valider le setup. 
- Le deuxième jour (J2) porte sur l’analyse, notamment à travers des tests des vulnérabilités de l’OWASP Top 10, afin d’obtenir des premiers résultats. 
- Le troisième jour (J3) est dédié à l’exploitation des failles identifiées, comme les injections SQL, les XSS ou encore les IDOR, avec la réalisation de preuves de concept (PoC). 
- Le quatrième jour (J4) est axé sur la défense, avec une analyse des logs pour détecter les attaques et proposer des recommandations de sécurité. 
- Enfin, le cinquième jour (J5) correspond à la finalisation du projet, avec la rédaction du rapport final et la préparation des supports de présentation (slides).

---

## 5. Livrables et Formats

- Rapport technique (10-15 pages)
- Rapport exécutif (1 page)
- Scripts utilisés
- Captures réseau
- Screenshots
- Présentation orale

---

## 6. Critères de Qualité

- Tests reproductibles
- Respect des standards OWASP
- Preuves techniques claires
- Explications compréhensibles
- Analyse réaliste des impacts

---

## Conclusion

Ce plan d’attaque permet d’avoir une approche structurée pour tester la sécurité des applications. Il couvre à la fois les attaques et la défense, ce qui est intéressant pour comprendre les deux aspects de la cybersécurité.
