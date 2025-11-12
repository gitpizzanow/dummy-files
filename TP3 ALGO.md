# Problème du Sac à Dos (Knapsack Problem)

## Introduction

Le problème du sac à dos consiste à sélectionner un ensemble d'objets ayant chacun un poids et une valeur (gain), de manière à maximiser la valeur totale tout en respectant une contrainte de capacité.

## Structure de Données

```python
class MonObjet:
    def __init__(self, numero, poids, gain):
        self.numero = numero
        self.poids = poids
        self.gain = gain
```

## Méthodes de Résolution

### 1. Méthode Récursive Naïve

**Complexité :** O(2ⁿ)

```python
def SAD(c: int, LO: list, indice: int):
    if c <= 0 or indice >= len(LO):
        return 0
    
    obj = LO[indice]
    
    if obj.poids > c:
        return SAD(c, LO, indice + 1)
    
    # Option 1: Prendre l'objet
    gain1 = obj.gain + SAD(c - obj.poids, LO, indice + 1)
    
    # Option 2: Ne pas prendre l'objet
    gain0 = SAD(c, LO, indice + 1)
    
    return max(gain0, gain1)
```

### 2. Méthode Exhaustive (Force Brute)

**Complexité :** O(2ⁿ)

```python
from itertools import combinations

def sac_a_dos_exhaustif(objets, capacite):
    meilleur_gain = 0
    meilleure_combinaison = []

    # Tester toutes les combinaisons possibles
    for i in range(1, len(objets) + 1):
        for subset in combinations(objets, i):
            poids_total = sum(o.poids for o in subset)
            gain_total = sum(o.gain for o in subset)

            if poids_total <= capacite and gain_total > meilleur_gain:
                meilleur_gain = gain_total
                meilleure_combinaison = subset

    return meilleur_gain, meilleure_combinaison
```

### 3. Programmation Dynamique (Bottom-Up)

**Complexité :** O(n × C) où n = nombre d'objets, C = capacité

```python
def sac_a_dos_DP(objets, capacite):
    n = len(objets)
    # Création de la table DP
    DP = [[0] * (capacite + 1) for _ in range(n + 1)]

    # Remplissage de la table
    for i in range(1, n + 1):
        for w in range(1, capacite + 1):
            if objets[i-1].poids <= w:
                # Choisir ou ignorer l'objet
                DP[i][w] = max(
                    objets[i-1].gain + DP[i-1][w - objets[i-1].poids],
                    DP[i-1][w]
                )
            else:
                # Impossible d'ajouter cet objet
                DP[i][w] = DP[i-1][w]

    return DP[n][capacite]
```

### 4. Récursion avec Mémoïsation (Top-Down)

**Complexité :** O(n × C)

```python
def sac_a_dos_recursif(objets, n, capacite, memo):
    # Cas de base
    if n == 0 or capacite == 0:
        return 0

    # Vérifier si déjà calculé
    if (n, capacite) in memo:
        return memo[(n, capacite)]

    objet = objets[n - 1]

    if objet.poids > capacite:
        result = sac_a_dos_recursif(objets, n - 1, capacite, memo)
    else:
        # Choisir ou ignorer l'objet
        inclure = objet.gain + sac_a_dos_recursif(objets, n - 1, capacite - objet.poids, memo)
        exclure = sac_a_dos_recursif(objets, n - 1, capacite, memo)
        result = max(inclure, exclure)

    memo[(n, capacite)] = result
    return result

# Utilisation
memo = {}
resultat = sac_a_dos_recursif(LO, len(LO), 10, memo)
```

### 5. Version Complète (avec Objets Sélectionnés)

Cette version retourne à la fois le gain maximal et la liste des objets sélectionnés.

```python
def SAD_Complete(C, LO, Indice=0):
    if Indice == len(LO):
        return 0, []

    # Option 1: Ne pas prendre l'objet
    G1, L1 = SAD_Complete(C, LO, Indice + 1)

    # Option 2: Prendre l'objet s'il entre dans le sac
    if LO[Indice].poids <= C:
        G2, L2 = SAD_Complete(C - LO[Indice].poids, LO, Indice + 1)
        G2 += LO[Indice].gain
        L2 = L2 + [LO[Indice].numero]
    else:
        G2, L2 = 0, []

    # Retourner la meilleure option
    if G1 > G2:
        return G1, L1
    else:
        return G2, L2
```

**Exemple d'utilisation :**

```python
LO = [
    MonObjet(1, 2, 7),
    MonObjet(2, 3, 6),
    MonObjet(3, 4, 10),
    MonObjet(4, 5, 8)
]

print(SAD_Complete(5, LO))   # Output: (15, [4, 1])
print(SAD_Complete(10, LO))  # Output: (27, [4, 3, 1])
```

## Comparaison des Méthodes

| Méthode                  | Type           | Complexité | Mémoire | Idée principale                |
|--------------------------|----------------|------------|---------|--------------------------------|
| **Exhaustive**           | Brute force    | O(2ⁿ)      | Faible  | Tester toutes les combinaisons |
| **Récursive + mémo**     | Top-down (DP)  | O(n×C)     | Moyenne | Sauvegarde des sous-résultats  |
| **Itérative (table DP)** | Bottom-up (DP) | O(n×C)     | Moyenne | Construction d'une table DP    |

## Benchmark de Performance

Code pour tester les performances selon la taille du problème :

```python
import random
import time

for n in range(4, 400):
    LO = []
    for i in range(1, n + 1):
        LO.append(MonObjet(i, random.randint(1, 10), random.randint(1, 10)))

    start = time.time()
    g = SAD(1000, LO, 0)
    t = time.time() - start
    print(f"|LO|={len(LO)}, elapsed time = {t:.4f} seconds")
```

## Recommandations

- **Petites instances (n < 20)** : Méthode exhaustive acceptable
- **Instances moyennes (20 < n < 1000)** : Programmation dynamique recommandée
- **Grandes instances** : Algorithmes d'approximation ou heuristiques nécessaires

## Limitations

- Les méthodes de programmation dynamique ont une complexité pseudo-polynomiale
- La mémoire requise peut devenir importante pour de grandes capacités
- Pour le problème du sac à dos fractionnaire, un algorithme glouton en O(n log n) suffit
