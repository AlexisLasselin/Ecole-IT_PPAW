# Liste des vulnérabilités trouvées sur Juice Shop

## VULN-001 : SQL Injection dans le champ de login

**Sévérité** : Élevée

**Description** : Une injection SQL peut être exploitée via le champ de login pour accéder au compte de n'importe quel utilisateur sans connaître son mot de passe. Il suffit d'entrer une adresse mail valide, visible avec les reviews.

**Procédure** : ![Adresse email de l'administrateur exposée](../evidence/juice_shop/exposed_admin_address.png)

![Injection SQL Reussie](../evidence/juice_shop/admin_login_sql_injection.png)

**Impact** :

    - Accès non autorisé au compte administrateur
    - Compromission totale de l'application
    - Exfiltration de données sensibles
    - Potentiel de modification ou suppression de données
    - Utilisation de l'application pour des attaques ultérieures (pivoting)
    - Perte de confiance des utilisateurs et atteinte à la réputation de l'entreprise
    - Responsabilité légale en cas de violation de données personnelles

**Recommandations** :

    - Utiliser des requêtes préparées (prepared statements) pour toutes les interactions avec la base de données
    - Valider et assainir toutes les entrées utilisateur
    - Mettre en place une politique de gestion des erreurs qui ne divulgue pas d'informations sensibles
    - Effectuer des tests de sécurité réguliers pour identifier et corriger les vulnérabilités

## VULN-002 : Brute Force sur le champ de login

**Sévérité** : Élevée

**Description** : Le champ de login est vulnérable à une attaque de force brute, permettant à un attaquant de tenter de nombreuses combinaisons de mots de passe sans restriction.

**Procédure** : En utilisant un outil de brute force (comme Hydra ou Burp Suite), un attaquant peut automatiser les tentatives de connexion en essayant différentes combinaisons de mots de passe pour un compte utilisateur ou administrateur.

**Procédure** :

<!-- TODO -->

**Impact** :

    - Accès non autorisé à des comptes utilisateurs ou administrateurs
    - Compromission de l'application et des données sensibles
    - Potentiel de modification ou suppression de données
    - Utilisation de l'application pour des attaques ultérieures (pivoting)
    - Perte de confiance des utilisateurs et atteinte à la réputation de l'entreprise
    - Responsabilité légale en cas de violation de données personnelles

**Recommandations** :

    - Mettre en place une politique de verrouillage de compte après un certain nombre de tentatives de connexion échouées
    - Utiliser des CAPTCHA pour empêcher les attaques automatisées
    - Implémenter une authentification à deux facteurs (2FA) pour renforcer la sécurité des comptes
    - Surveiller les tentatives de connexion et alerter en cas d'activité suspecte
    - Forcer l'utilisation de mots de passe forts et uniques pour tous les comptes utilisateurs et administrateurs
