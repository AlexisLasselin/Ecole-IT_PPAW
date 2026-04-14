# Liste des vulnérabilités trouvées

---

Juice Shop est une application web intentionnellement vulnérable, conçue pour l'apprentissage de la sécurité informatique. Voici une liste des vulnérabilités identifiées lors de l'analyse de l'application :

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

![Brute Force sur le champ de login](../evidence/juice_shop/bruteforce_preparation.png)

![Résultat du Brute Force](../evidence/juice_shop/bruteforce_results.png)

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

## VULN-003 : Exposition de documents sensibles

**Sévérité** : Moyenne

**Description** : Des documents sensibles, tels que les conditions générales de vente (CGV), sont accessibles sans authentification, ce qui peut entraîner la divulgation d'informations confidentielles.

**Procédure** : En accédant à l'URL spécifique des CGV, un utilisateur non authentifié peut visualiser et télécharger ces documents.

**Procédure** :

![Accès aux CGU sans authentification](../evidence/juice_shop/exposed_documents.png)

![Contenu du dossier exposé](../evidence/juice_shop/easy_access_folder.png)

![Document sensible exposé](../evidence/juice_shop/confidential_document_exposed.png)

**Impact** :

    - Divulgation d'informations confidentielles
    - Potentiel de violation de la vie privée des utilisateurs
    - Atteinte à la réputation de l'entreprise
    - Responsabilité légale en cas de violation de données personnelles

**Recommandations** :

    - Restreindre l'accès aux documents sensibles en exigeant une authentification
    - Mettre en place des contrôles d'accès basés sur les rôles pour limiter l'accès aux informations sensibles
    - Assurer la sécurité des documents en les stockant dans un emplacement sécurisé et en utilisant des permissions appropriées
    - Effectuer des audits réguliers pour identifier et corriger les vulnérabilités d'exposition de données