# Documentation des conventions du projet

Ce document définit les conventions de codage et de documentation du projet afin d’assurer la cohérence, la lisibilité et la maintenabilité du code.
Ces règles doivent être respectées par tous les contributeurs.

## 1. Structure du projet

Le projet doit suivre une organisation claire et standardisée :

```bash
project/
│
├── src/            # Code source principal
├── tests/          # Tests unitaires
├── docs/           # Documentation
├── README.md       # Présentation du projet
├── requirements.txt
└── .gitignore
```

## 2. Conventions de fichiers et dossiers

### Nommage

- Les fichiers et dossiers doivent être en **snake_case** :
  - `mon_fichier.py`
  - `api_client/`

- Les noms doivent être :
  - descriptifs
  - explicites
  - courts mais compréhensibles

### Bonnes pratiques

- `connexion_api.py` → clair et précis
- `utils/` → acceptable pour des fonctions utilitaires

### Mauvaises pratiques

- `main2.py` → non descriptif
- `projet/` → trop générique

## 3. Conventions de codage

Le code doit être clair, lisible et maintenable.

### Règles générales

- Respecter **PEP 8**

- Utiliser des noms explicites :
  - `calculer_somme()`
  - `nombre_utilisateurs`

- Limiter la longueur des lignes :
  - idéalement **88 caractères**

- Éviter la duplication de code

- Favoriser les fonctions courtes et modulaires

## 4. Conventions d’écriture du code

### Exemple

```python
PRIX_TAXE = 0.2

def calculer_prix_total(prix: float) -> float:
    """Calcule le prix total en ajoutant la taxe.

    Args:
        prix (float): Prix initial.

    Returns:
        float: Prix avec taxe.
    """
    return prix + (prix * PRIX_TAXE)
```

### Constantes

- Toujours en **MAJUSCULES**
- Définies en haut du fichier ou dans `constants.py`

## 5. Imports

Les imports doivent être organisés comme suit :

```python
# Standard library
import os
import sys

# Librairies externes
import requests

# Modules internes
from src.utils import helper
```

### Règles d'import

- Regrouper par catégorie
- Trier par ordre alphabétique
- Éviter `import *`

## 6. Gestion des exceptions

- Toujours gérer les erreurs de manière explicite

### Bon exemple

```python
try:
    valeur = int(input())
except ValueError:
    print("Entrée invalide")
```

### Mauvaise pratique

```python
try:
    ...
except Exception:
    pass
```

## 7. Tests unitaires

Les tests sont obligatoires pour garantir la qualité du code.

### Organisation

```bash
tests/
    test_api.py
    test_utils.py
```

### Règles de test

- Utiliser **pytest**
- Nommer les fichiers : `test_*.py`
- Tester les cas :
  - normaux
  - limites
  - erreurs

## 8. Documentation

La documentation doit être :

- claire
- concise
- à jour

### Bonnes pratiques de documentation

- Utiliser des titres et sous-titres
- Structurer avec des listes
- Ajouter des exemples
- Mettre en évidence les éléments importants

## 9. Conventions d’écriture de documentation

### Exemple de formatage

````markdown
# Titre principal

## Sous-titre

- Point important 1
- Point important 2

Exemple d'utilisation :

```python
prix = 100
prix_total = calculer_prix_total(prix)
print(prix_total)  # 120.0
```
````

## 10. Commentaires

- Expliquer **le pourquoi**, pas le quoi
- Éviter les commentaires inutiles

### Mauvais exemple de commentaire

```python
# Ajoute 1
x = x + 1
```

### Bon exemple de commentaire

```python
# Correction d'un bug lié à un arrondi
x = x + 1
```

## 11. Bonnes pratiques générales

- Écrire du code simple avant du code optimisé
- Relire son code avant commit
- Maintenir une cohérence dans tout le projet
- Mettre à jour la documentation à chaque modification

## Conclusion

Le respect de ces conventions garantit :

- une meilleure lisibilité du code
- une collaboration efficace
- une maintenance simplifiée

Chaque contributeur est responsable de leur application.
