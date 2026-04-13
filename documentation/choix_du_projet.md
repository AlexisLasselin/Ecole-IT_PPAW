# Projet choisi

## Projet 2 : Red Team vs Blue Team - Infrastructure E-commerce

### 3 arguments techniques justifiant le choix

1. **Environnement réaliste et complet (attaque + défense)**
   Ce projet couvre à la fois les aspects offensifs (exploitation de vulnérabilités web et API) et défensifs (détection via SIEM), ce qui correspond aux pratiques actuelles en cybersécurité (SOC, pentest, DevSecOps).

2. **Disponibilité d’outils et de ressources pédagogiques**
   Les applications fournies (Juice Shop, crAPI, VAmPI) sont conçues pour l’apprentissage avec des vulnérabilités documentées, ce qui permet une progression structurée et évite les blocages liés à des environnements trop complexes.

3. **Forte composante pratique et automatisation**

   Le projet permet de développer des scripts d’attaque (Python) et des règles de détection (Wazuh/ELK), renforçant les compétences techniques concrètes recherchées en entreprise.

### 2 risques identifiés et stratégies de mitigation

1. **Complexité de l’intégration des outils (Docker, SIEM, logs)**

   Impact : perte de temps lors de la mise en place de l’environnement

   Mitigation :

   utiliser des configurations Docker Compose existantes

   tester chaque composant individuellement avant intégration

   documenter chaque étape

2. **Difficulté à corréler attaques et détection**

   Impact : projet incomplet (attaque sans preuve de détection)

   Mitigation :

   planifier les attaques en parallèle avec la mise en place des logs

   tester chaque attaque avec vérification immédiate côté SIEM

   créer une matrice "attaque → log → alerte"

### Compétences mobilisées et lacunes éventuelles

**Compétences mobilisées :**

- Sécurité web (XSS, SQL Injection, IDOR)
- Analyse de logs et détection d’incidents
- Utilisation d’outils SIEM (Wazuh, ELK)
- Développement de scripts Python
- Conteneurisation avec Docker

**Lacunes identifiées :**

- Création avancée de règles SIEM personnalisées
- Automatisation avancée des attaques (framework structuré)
- Visualisation avancée (dashboards complexes)

**Plan d’amélioration :**

- s’appuyer sur la documentation officielle
- utiliser des exemples existants
- progresser par itérations simples

### Estimation macro du planning

| Phase                         | %Temps estimé |
| ----------------------------- | ------------- |
| Installation \& Setup         | 20%           |
| Attaques (Red Team)           | 25%           |
| Automatisation (scripts)      | 15%           |
| Détection (Blue Team)         | 20%           |
| Dashboard \& Visualisation    | 10%           |
| Documentation \& finalisation | 10%           |

### Alignement avec le projet de carrière

Ce projet est en adéquation avec un objectif professionnel orienté cybersécurité (SOC, analyste sécurité, pentester). Il permet de développer une vision globale des attaques et de la défense, compétence essentielle en environnement professionnel.
