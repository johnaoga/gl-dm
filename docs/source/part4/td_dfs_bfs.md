(td_dfs_bfs)=

# Travaux Dirigés : Exploration des espaces de possibilités pour le Data Mining — parcours DFS et BFS

**Cours :** Data Mining / Fouille de données  
**Thème :** Exploration d'espaces de possibilités — parcours en profondeur (DFS) et en largeur (BFS), application à la génération du treillis d'itemsets  
**Modalité :** individuel
**Durée indicative :** 4 heures 
**Date limite de remise :** 16/10/2026 à 18h00  
**Lien de remise (Google Form) :** <https://forms.gle/eyVvZseD29Sarb7y9>

---

## 1. Contexte

Fouiller des données consiste souvent à explorer un espace de solutions combinatoires qui peut être astronomiquement grand. En extraction d'itemsets fréquents, par exemple, l'espace de toutes les combinaisons d'items possibles forme un *treillis* (lattice) dont la taille est exponentielle dans le nombre d'items. La capacité à parcourir cet espace de façon systématique — en évitant les redondances et les branches inutiles — est au cœur de tous les algorithmes modernes de Data Mining.

Ce TD vous amène, pas à pas, à construire cette intuition depuis les bases : vous commencerez par les arbres sur papier, vous implémenterez les parcours DFS et BFS en boucle puis de façon récursive, vous générerez le treillis complet d'un alphabet, et vous comparerez plusieurs stratégies d'implémentation de l'algorithme Apriori.

## 2. Objectifs pédagogiques

À l'issue de ce TD, vous devez être capable de :

1. **Identifier et nommer** les composants d'un arbre (racine, nœuds internes, feuilles, enfants, parent, profondeur, niveau).
2. **Effectuer à la main** les quatre variantes de parcours (DFS gauche-droite, DFS droite-gauche, BFS gauche-droite, BFS droite-gauche) et en déduire l'état de la pile ou de la file à chaque itération.
3. **Implémenter** DFS et BFS en boucle avec une pile et une file, puis extraire une fonction générale permettant d'obtenir l'un ou l'autre sans utiliser de `if`.
4. **Écrire** une fonction récursive, identifier base case et cas récursif, observer la pile d'exécution, puis implémenter le DFS récursif.
5. **Relier** la notion de parcours à la génération du treillis des sous-ensembles d'un alphabet et **caractériser** la structure combinatoire de ce treillis.
6. **Comparer** deux stratégies DFS (naïve vs heuristique d'ordre) en termes de nœuds visités et de temps d'exécution, et **justifier théoriquement** les résultats.
7. **Implémenter** l'algorithme Apriori dans une version DFS et une version BFS, comparer leurs performances, et les confronter à des implémentations de référence.

## 3. Prérequis techniques

- **Python 3.8+** (recommandé) **ou R** avec les bibliothèques : `matplotlib` / `ggplot2` pour les graphiques, `collections.deque` (Python standard) ou équivalent R pour la file. La bibliothèque `mlxtend` (Python) ou `arules` (R) servira de référence à la Partie G.
- **SPMF** (dernière version) : <https://www.philippe-fournier-viger.com/spmf/>. Vérifiez que Java est disponible avec `java -version` (version 8 ou plus récente).
- Un IDE ou éditeur de texte avec un terminal.
- Papier et stylo pour les parties A et E (obligatoires, prenez des photos de vos schémas).

> **Conseil général :** prenez des notes et des captures d'écran (ou photos) au fur et à mesure. Plusieurs questions du formulaire de remise vous demanderont des valeurs précises (ordres de traversée, contenus de pile/file à une itération donnée, nombre de nœuds visités) que vous ne pourrez obtenir qu'en ayant réellement réalisé les manipulations.

---

## 4. Travail à réaliser

### Partie A — Arbres et terminologie

**Objectif :** manipuler physiquement un arbre, fixer le vocabulaire, et effectuer les quatre variantes de parcours.

1. **Sur papier**, dessinez l'arbre suivant (respectez exactement la structure) :

   ```
              R
             / \
            A   B
           / \   \
          C   D   E
         /
        F
   ```

   Identifiez et annotez sur votre dessin :
   - la **racine** (*root*) ;
   - les **feuilles** (*leaves*) — nœuds sans enfants ;
   - les **nœuds internes** — nœuds avec au moins un enfant ;
   - pour chaque nœud non-racine, son **parent** ;
   - la **profondeur** (*depth*) de chaque nœud (racine = profondeur 0) ;
   - le **niveau** (*level*) de chaque nœud et le nombre de nœuds à chaque niveau.

2. **Parcours DFS gauche-droite (DFS-GD) :** en partant de R, visitez toujours d'abord l'enfant le plus à gauche avant d'explorer la droite. Notez l'ordre complet de visite des nœuds.

3. **Parcours DFS droite-gauche (DFS-DG) :** en partant de R, visitez toujours d'abord l'enfant le plus à droite. Notez l'ordre complet.

4. **Parcours BFS gauche-droite (BFS-GD) :** visitez tous les nœuds de chaque niveau de gauche à droite avant de passer au niveau suivant. Notez l'ordre complet.

5. **Parcours BFS droite-gauche (BFS-DG) :** même principe, mais de droite à gauche à chaque niveau. Notez l'ordre complet.

   > **À vérifier :** le 4e nœud visité dans DFS-GD doit être F. Le 4e nœud visité dans BFS-GD doit être C.

6. Répondez par écrit : en quoi le DFS et le BFS diffèrent-ils dans leur manière d'explorer l'arbre ? Qu'est-ce que cela implique pour la mémoire utilisée ?

---

### Partie B — Implémentation conceptuelle : Pile et File

**Objectif :** comprendre *pourquoi* une pile (LIFO) produit le DFS et une file (FIFO) produit le BFS, en simulant les étapes à la main.

#### B.1 DFS avec une Pile

L'algorithme en boucle utilise une **pile** (Last In, First Out — LIFO) :

```
INITIALISER pile avec [Racine]
TANT QUE pile non vide :
    node ← DÉPILER (pop) le sommet de la pile
    VISITER node
    POUR chaque enfant de node (dans l'ordre que vous choisissez) :
        EMPILER (push) l'enfant
```

1. Simulez **pas à pas** l'algorithme DFS sur l'arbre de la Partie A, en visant le parcours **gauche-droite**.  
   Pour obtenir DFS-GD avec une pile, réfléchissez dans quel ordre empiler les enfants (indice : pour que l'enfant gauche soit dépilé en premier, il doit être au sommet de la pile).

   Remplissez le tableau suivant (ajoutez les lignes nécessaires) :

   | Itération | Nœud dépilé | Nœuds empilés | Contenu de la pile après (sommet à droite) |
   |-----------|-------------|---------------|---------------------------------------------|
   | 1 | R | B, A | [B, A] |
   | 2 | A | D, C | [B, D, C] |
   | 3 | ? | ? | ? |
   | 4 | ? | — | ? |
   | 5 | ? | — | ? |
   | 6 | ? | ? | ? |
   | 7 | ? | — | ? |

   > **À noter :** relevez le contenu exact de la pile **après l'itération 3**. Vous en aurez besoin dans le formulaire.

2. Refaites la simulation pour **DFS droite-gauche**. Quelle est la seule modification à apporter à l'ordre d'empilement ?

#### B.2 BFS avec une File

L'algorithme en boucle utilise une **file** (First In, First Out — FIFO) :

```
INITIALISER file avec [Racine]
TANT QUE file non vide :
    node ← DÉFILER (dequeue) l'élément en tête
    VISITER node
    POUR chaque enfant de node (dans l'ordre que vous choisissez) :
        ENFILER (enqueue) l'enfant en queue
```

1. Simulez **pas à pas** ce BFS-GD sur l'arbre de la Partie A.

   | Itération | Nœud défilé | Nœuds enfilés | Contenu de la file après (tête à gauche) |
   |-----------|-------------|---------------|------------------------------------------|
   | 1 | R | A, B | [A, B] |
   | 2 | A | C, D | [B, C, D] |
   | 3 | ? | ? | ? |
   | 4 | ? | ? | ? |
   | 5 | ? | — | ? |
   | 6 | ? | — | ? |
   | 7 | ? | — | ? |

   > **À noter :** relevez le contenu exact de la file **après l'itération 2**. Vous en aurez besoin dans le formulaire.

#### B.3 Pseudo-code général et analyse

1. Écrivez un **pseudo-code unique** qui produit soit un DFS soit un BFS selon la structure de données utilisée. Identifiez la seule ligne qui change entre les deux.

2. Comment modifier ce pseudo-code pour contrôler l'ordre gauche-droite vs droite-gauche **avec le moins d'effort possible** (une seule ligne, sans `if`) ?

3. Que se passerait-il si on ajoutait les nœuds **en tête** de la file (au lieu de la queue) ? Quel parcours obtiendrait-on ?

---

### Partie C — Implémentation en Python ou R

**Objectif :** traduire le pseudo-code de la Partie B en code exécutable et factoriser dans une fonction générale.

#### C.1 Représentation de l'arbre

Représentez l'arbre de la Partie A comme un dictionnaire (Python) ou une liste nommée (R) :

```python
# Python — exemple de structure (à compléter ou adapter)
tree = {
    'R': ['A', 'B'],
    'A': ['C', 'D'],
    'B': ['E'],
    'C': ['F'],
    'D': [],
    'E': [],
    'F': []
}
```

#### C.2 Fonctions DFS et BFS en boucle

1. Écrivez une fonction `dfs_loop(tree, root, left_to_right=True)` qui utilise une pile et retourne la liste des nœuds visités dans l'ordre.
2. Écrivez une fonction `bfs_loop(tree, root, left_to_right=True)` qui utilise une file (`collections.deque` en Python, ou équivalent) et retourne la liste des nœuds visités.
3. Vérifiez que les quatre parcours (DFS-GD, DFS-DG, BFS-GD, BFS-DG) correspondent exactement à vos résultats papier de la Partie A.

#### C.3 Fonction générale sans `if`

1. Écrivez une fonction `traverse(tree, root, use_queue=False, left_to_right=True)` qui implémente **à la fois** DFS et BFS **sans aucun `if`** sur le type de parcours. Le seul paramètre qui différencie les deux est la méthode d'extraction de l'élément suivant.

   > **Indice :** `collections.deque` supporte `pop()` (fin, LIFO) et `popleft()` (début, FIFO). Stockez la méthode d'extraction dans une variable avant la boucle.

2. Réécrivez `dfs_loop` et `bfs_loop` en appelant `traverse`, sans dupliquer de logique.

3. Ajoutez un paramètre `left_to_right` à `traverse` et montrez comment il change l'ordre des enfants ajoutés, sans `if` dans la boucle (une ligne suffit avec `reversed()` ou le slicing `[::-1]`).

---

### Partie D — Fonctions récursives et DFS récursif

**Objectif :** comprendre la récursion à travers des exemples classiques, puis implémenter le DFS récursif et observer la pile d'exécution.

#### D.1 Découverte de la récursion

1. Copiez les deux fonctions suivantes dans votre environnement :

   ```python
   def factorielle(n):
       # Base case
       if n == 0:
           return 1
       # Cas récursif
       return n * factorielle(n - 1)

   def fibonacci(n):
       # Base case
       if n <= 1:
           return n
       # Cas récursif
       return fibonacci(n - 1) + fibonacci(n - 2)
   ```

2. Sur chaque fonction, identifiez et annotez :
   - le ou les **cas de base** (*base case*) : la condition qui arrête la récursion ;
   - le **cas récursif** : l'appel à la fonction elle-même avec des paramètres plus petits ;
   - la **valeur retournée** à chaque niveau.

3. Exécutez `factorielle(4)` et `fibonacci(5)`. Pour chacune, **tracez à la main** (ou avec un outil de débogage) la **pile des appels** (*call stack*) : dessinez l'empilement et le dépliement des appels récursifs.

4. Exécutez en Python le code suivant pour visualiser les appels récursifs de `factorielle` :

   ```python
   import sys

   def factorielle_trace(n, profondeur=0):
       print("  " * profondeur + f"factorielle({n}) appelée")
       if n == 0:
           print("  " * profondeur + "→ retourne 1 (base case)")
           return 1
       resultat = n * factorielle_trace(n - 1, profondeur + 1)
       print("  " * profondeur + f"→ retourne {n} × ... = {resultat}")
       return resultat

   factorielle_trace(5)
   ```

   Observez la sortie. Qu'est-ce que la profondeur d'indentation représente ?

#### D.2 DFS récursif

1. Écrivez la fonction `dfs_recursive(tree, node, visited=None)` qui parcourt l'arbre en DFS-GD de façon récursive. Elle doit retourner la liste des nœuds visités.

   ```python
   def dfs_recursive(tree, node, visited=None):
       if visited is None:
           visited = []
       # À compléter : visiter node, puis appeler récursivement sur chaque enfant
       ...
       return visited
   ```

2. Ajoutez un paramètre `count` (ou une variable dans une liste pour contourner la portée de Python) qui incrémente à chaque appel récursif. Affichez le nombre total de nœuds visités.

3. Vérifiez que le résultat de `dfs_recursive` correspond au résultat de `dfs_loop` pour le même arbre.

4. **Question de réflexion :** la pile d'appels du système (gérée par Python) joue le même rôle que la pile explicite de la Partie B. Expliquez cette correspondance en une ou deux phrases.

---

### Partie E — Du parcours au treillis des itemsets

**Objectif :** relier les parcours d'arbre à la structure combinatoire qui sous-tend l'extraction d'itemsets fréquents.

#### E.1 Générer le treillis sur papier

1. Prenez un alphabet de **6 éléments** : Σ = {a, b, c, d, e, f}.

2. **Sur papier**, générez le **treillis de tous les sous-ensembles** (*power set lattice*) de Σ. Organisez-les par **niveau** (taille du sous-ensemble : 0, 1, 2, …, 6). Reliez chaque sous-ensemble à ses enfants immédiats (les sous-ensembles qui ajoutent exactement un élément).

   > **Conseil :** commencez par le sous-ensemble vide {} au niveau 0, puis listez tous les singletons au niveau 1, toutes les paires au niveau 2, etc. Utilisez un ordre alphabétique cohérent dans chaque niveau.

3. Comptez le nombre d'éléments à chaque niveau et remplissez le tableau :

   | Niveau | Taille des sous-ensembles | Nombre d'éléments |
   |--------|--------------------------|-------------------|
   | 0 | 0 | 1 |
   | 1 | 1 | ? |
   | 2 | 2 | ? |
   | 3 | 3 | ? |
   | 4 | 4 | ? |
   | 5 | 5 | ? |
   | 6 | 6 | ? |
   | **Total** | | ? |

4. **Questions d'analyse :**
   a. Combien y a-t-il d'éléments en tout dans le treillis ?
   b. Quel niveau contient le plus d'éléments ? Y a-t-il un autre niveau avec exactement le même nombre ?
   c. Les nombres d'éléments à chaque niveau vous rappellent-ils quelque chose vu en cours de mathématiques ? Nommez la structure mathématique ou la formule correspondante (indice : pensez aux combinaisons C(n, k)).

#### E.2 Éléments aux positions i et j dans le treillis ordonné BFS

En supposant que vous listiez les éléments du treillis **niveau par niveau** (comme en BFS), en respectant l'ordre alphabétique au sein de chaque niveau :

1. Quel est le 8e élément listé (en comptant à partir de 1) ?
2. Quel est le 23e élément listé ?

   > **À vérifier avant de continuer :** le 8e élément doit être {a, b} et le 23e doit être le premier itemset de taille 3 dans l'ordre alphabétique.

#### E.3 Représenter le treillis comme un arbre de parcours

1. Le treillis n'est pas un arbre (un sous-ensemble peut avoir plusieurs parents). Pour le parcourir comme un arbre avec DFS sans visiter deux fois le même élément, utilisez l'**heuristique d'ordre** : depuis un nœud courant {x₁, x₂, …, xₖ}, n'étendre qu'avec des éléments d'indice strictement supérieur au dernier élément xₖ (en ordre alphabétique).

2. Tracez le début de cet arbre DFS sur papier, en partant de la racine {} et en montrant au moins 3 niveaux.

3. Listez les 15 premiers éléments dans l'ordre DFS-GD avec heuristique d'ordre :

   | Position | Itemset |
   |----------|---------|
   | 1 | {} |
   | 2 | {a} |
   | 3 | {a, b} |
   | 4 | {a, b, c} |
   | 5 | {a, b, c, d} |
   | 6 | {a, b, c, d, e} |
   | 7 | ? |
   | 8 | ? |
   | 9 | ? |
   | 10 | ? |
   | 11 | ? |
   | 12 | ? |
   | 13 | ? |
   | 14 | ? |
   | 15 | ? |

   > **À vérifier :** la position 7 doit être {a, b, c, d, e, f} et la position 12 doit être {a, b, d}.

4. Quel est le 20e élément visité en DFS-GD avec heuristique d'ordre ?

---

### Partie F — Explorer le treillis avec le DFS : naïf vs heuristique d'ordre

**Objectif :** implémenter deux versions du DFS sur le treillis et mesurer l'impact de l'heuristique d'ordre sur le nombre de nœuds visités et le temps d'exécution.

#### F.1 Version naïve

La version naïve étend le nœud courant avec **n'importe quel** élément de l'alphabet qui n'est pas encore dans l'itemset courant, sans contrainte d'ordre. Cela conduit à visiter le même sous-ensemble plusieurs fois (via des chemins différents).

1. Implémentez la fonction récursive `dfs_naive(alphabet, current, all_itemsets, counter)` qui :
   - ajoute `frozenset(current)` à `all_itemsets` ;
   - incrémente `counter[0]` (ou un compteur mutable) ;
   - pour chaque item de `alphabet` absent de `current`, s'appelle récursivement.

   ```python
   def dfs_naive(alphabet, current, all_itemsets, counter):
       all_itemsets.append(frozenset(current))
       counter[0] += 1
       for item in alphabet:
           if item not in current:
               dfs_naive(alphabet, current + [item], all_itemsets, counter)
   ```

2. Exécutez pour `alphabet = ['a', 'b', 'c', 'd', 'e', 'f']`. Combien de nœuds sont visités ? Combien d'itemsets distincts sont générés (dédoublonnés) ?

#### F.2 Version avec heuristique d'ordre

1. Implémentez `dfs_ordered(alphabet, current, last_idx, all_itemsets, counter)` qui n'étend qu'avec des éléments d'indice strictement supérieur à `last_idx` dans l'alphabet :

   ```python
   def dfs_ordered(alphabet, current, last_idx, all_itemsets, counter):
       all_itemsets.append(frozenset(current))
       counter[0] += 1
       for i in range(last_idx + 1, len(alphabet)):
           dfs_ordered(alphabet, current + [alphabet[i]], i, all_itemsets, counter)
   ```

2. Exécutez pour le même alphabet. Combien de nœuds sont visités ?

#### F.3 Comparaison expérimentale

1. Mesurez le **nombre de nœuds visités** et le **temps d'exécution** des deux versions pour des tailles d'alphabet croissantes. Commencez à n = 5, augmentez jusqu'à atteindre un temps d'exécution d'**au moins 1 minute** pour la version naïve. Fixez un **timeout de 10 minutes** : si une exécution dépasse ce seuil, arrêtez-la et notez "timeout" dans vos mesures — c'est un résultat à rapporter.

   | n (taille alphabet) | Nœuds naïf | Temps naïf (s) | Nœuds ordonné | Temps ordonné (s) |
   |--------------------|-----------|----------------|---------------|-------------------|
   | 5 | ? | ? | ? | ? |
   | 6 | ? | ? | ? | ? |
   | 8 | ? | ? | ? | ? |
   | 10 | ? | ? | ? | ? |
   | 12 | ? | ? | ? | ? |
   | … | … | … | … | … |

   > **Conseil :** utilisez `time.perf_counter()` en Python ou `proc.time()` en R pour mesurer le temps. Répétez chaque mesure 3 fois et retenez la médiane.

2. Tracez deux graphiques (en échelle log si nécessaire) :
   - Nombre de nœuds visités en fonction de n (naïf vs ordonné).
   - Temps d'exécution en fonction de n (naïf vs ordonné).

#### F.4 Justification théorique

1. Pour la version **naïve** : exprimez le nombre total de nœuds visités en fonction de n. Ce nombre inclut les visites redondantes (même sous-ensemble atteint par plusieurs chemins). Donnez la formule exacte, puis calculez-la pour n = 6.

   > **Indice :** à la profondeur k dans l'arbre naïf, le nombre de nœuds est n × (n–1) × … × (n–k+1) = P(n, k). Sommez sur tous les niveaux.

2. Pour la version **avec heuristique d'ordre** : exprimez le nombre total de nœuds visités en fonction de n. Chaque sous-ensemble est visité exactement une fois.

   > **Indice :** le nombre de sous-ensembles de taille k est C(n, k). Sommez sur tous les niveaux.

3. Vérifiez que vos formules correspondent aux valeurs mesurées expérimentalement.

---

### Partie G — Algorithme Apriori : version DFS et version BFS

**Objectif :** implémenter Apriori dans deux variantes (DFS et BFS), comparer leurs performances, et confronter les résultats à des implémentations de référence.

#### G.1 Données

Utilisez le **jeu de données retail** de SPMF (<https://www.philippe-fournier-viger.com/spmf/index.php?link=datasets.php>). Lisez le fichier et chargez-le en Python ou R comme une liste de sets (ensembles d'entiers).

```python
def load_transactions(filepath):
    transactions = []
    with open(filepath) as f:
        for line in f:
            items = set(map(int, line.strip().split()))
            transactions.append(items)
    return transactions
```

#### G.2 Apriori DFS

L'idée : partir de la racine (ensemble vide) et étendre en DFS avec heuristique d'ordre. À chaque nœud, calculer le support de l'itemset courant ; si ce support est inférieur à `min_support`, élaguer la branche entière (propriété antimonotone d'Apriori : si un itemset est non-fréquent, tous ses sur-ensembles le sont aussi).

1. Implémentez `apriori_dfs(transactions, alphabet, min_support)` :

   ```python
   def apriori_dfs(transactions, alphabet, min_support):
       n = len(transactions)
       frequent = []
       nodes_visited = [0]

       def support(itemset):
           return sum(1 for t in transactions if itemset.issubset(t)) / n

       def dfs(current, last_idx):
           nodes_visited[0] += 1
           sup = support(frozenset(current))
           if sup < min_support:
               return  # élagage
           if current:  # ne pas ajouter l'ensemble vide
               frequent.append((frozenset(current), sup))
           for i in range(last_idx + 1, len(alphabet)):
               dfs(current + [alphabet[i]], i)

       dfs([], -1)
       return frequent, nodes_visited[0]
   ```

2. Testez sur un petit jeu de données (par exemple les 5 premiers items distincts de retail) avec `min_support = 0.5`.

#### G.3 Apriori BFS (niveau par niveau)

La version BFS explore le treillis niveau par niveau : d'abord tous les 1-itemsets fréquents, puis les 2-itemsets, etc. Les candidats de niveau k+1 sont générés à partir des k-itemsets fréquents.

1. Implémentez `apriori_bfs(transactions, alphabet, min_support)` :

   ```python
   from itertools import combinations

   def apriori_bfs(transactions, alphabet, min_support):
       n = len(transactions)
       frequent = []
       nodes_visited = [0]

       def support(itemset):
           return sum(1 for t in transactions if itemset.issubset(t)) / n

       # Niveau k = 1 : singletons
       current_frequent = []
       for item in alphabet:
           nodes_visited[0] += 1
           sup = support(frozenset([item]))
           if sup >= min_support:
               current_frequent.append(frozenset([item]))
               frequent.append((frozenset([item]), sup))

       # Niveaux suivants
       while current_frequent:
           # Générer les candidats de taille k+1 (jointure)
           k = len(next(iter(current_frequent)))
           candidates = set()
           for i, s1 in enumerate(current_frequent):
               for s2 in current_frequent[i+1:]:
                   union = s1 | s2
                   if len(union) == k + 1:
                       candidates.add(union)
           next_frequent = []
           for candidate in candidates:
               nodes_visited[0] += 1
               sup = support(candidate)
               if sup >= min_support:
                   next_frequent.append(candidate)
                   frequent.append((candidate, sup))
           current_frequent = next_frequent

       return frequent, nodes_visited[0]
   ```

#### G.4 Comparaison expérimentale

> **Conseil :** pour faire des comparaisons expérimentales, l'idéal est d'avoir chaque algorithme qui peux se lancer indépendamment dans des scripts (e.g., apriori_dfs.py, apriori_bfs.py, ...) chacun de vos scripts pouvant recevoir des paramètres pour s'éxécuter. Ensuite, vous avez un script `runner.py` qui recoit la liste des scripts à executer, et la liste d'éxécution pour chaque paramètre (e.g., liste des supports), ce script lance chaque script et mesure ce que vous voulez et écrit tout dans un fichier csv. Enfin vous avez un dernier script `compare.py` qui vous permet de comparer les résultats à partir du fichier csv et faire des plots si nécessaire.

1. **Vérification de cohérence :** pour plusieurs valeurs de `min_support` (par exemple 0.01, 0.05, 0.10, 0.20, 0.50), vérifiez que `apriori_dfs` et `apriori_bfs` retournent le **même ensemble d'itemsets fréquents** (même itemsets, mêmes supports). Expliquez comment vous avez effectué cette vérification.

2. **Mesure de performance :** mesurez le temps d'exécution des deux versions pour au moins 5 valeurs de support sur le jeu *retail*. Construisez un graphique : support minimum en abscisse, temps d'exécution en ordonnée (une courbe par version). Si une exécution dépasse 5 minutes, arrêtez-la et reportez "timeout".

3. **Comparaison avec les références :**
   - **SPMF :** exécutez Apriori de SPMF pour les mêmes valeurs de support. Notez le temps affiché par SPMF.
   - **Bibliothèque Python ou R :** trouvez et utilisez une bibliothèque dédiée (`mlxtend.frequent_patterns.apriori` en Python ou `arules::apriori` en R). Vérifiez que les résultats sont cohérents.
   - Ajoutez ces deux références à votre graphique et commentez les différences de performance.

4. **Analyse :** expliquez théoriquement pourquoi les performances diffèrent. Quels sont les avantages respectifs d'une exploration DFS vs BFS dans le contexte d'Apriori (mémoire, élagage, génération de candidats) ?

---

## 5. Livrables

### 5.1 Le rapport

- **Une page de contenu** en **anglais** (format A4, police 11 pt minimum, marges raisonnables).
- **Jusqu'à 2 pages supplémentaires** réservées **uniquement** aux références, tableaux, figures et captures d'écran (ou photos des travaux papier).
- Format **PDF**, nommé `NOM_Prenom_TD_DFS_BFS.pdf` (ou `NOM1_NOM2_TD_DFS_BFS.pdf` en binôme).

**Ce qui est attendu dans la page de contenu :**

- Une brève analyse de la relation entre DFS/BFS et la structure en pile/file (2–3 phrases).
- Les formules théoriques du nombre de nœuds visités (naïf vs ordonné) et leur vérification expérimentale.
- L'analyse de la comparaison des temps d'exécution Apriori DFS vs BFS vs SPMF vs bibliothèque.
- Une réflexion sur l'impact de la stratégie d'exploration sur les performances en Data Mining.

Soyez **synthétique et précis** : une page, c'est court. Privilégiez les observations chiffrées et les arguments. Les figures doivent être lisibles, avoir des axes titrés avec unités, et être référencées dans le texte.

**Dans les pages annexes :**
- Les graphiques comparatifs des parties F et G.
- Les tableaux de mesures.
- Les photos des travaux papier (arbre Partie A, treillis Partie E).
- Les références bibliographiques.

### 5.2 Le formulaire de remise

La remise se fait **exclusivement** via le Google Form : <https://forms.gle/eyVvZseD29Sarb7y9>

Le formulaire contient des questions précises portant sur chaque partie (ordres de traversée, contenus de pile/file, formules, valeurs expérimentales, code) ainsi qu'un champ pour téléverser le rapport PDF. Vos réponses doivent être cohérentes avec votre rapport.

### 5.3 Critères d'évaluation (indicatifs)

- Exactitude des parcours papier et de la simulation pile/file
- Implémentation DFS et BFS en boucle, et fonction générale
- Implémentation récursive et correspondance avec la version itérative
- Analyse combinatoire du treillis et positions correctes
- Comparaison naïf vs ordonné : formules théoriques et graphiques
- Apriori DFS vs BFS : cohérence, performance, comparaison avec les références
- Qualité, concision et respect du format du rapport

---

## 6. Références

- P. Fournier-Viger et al., *SPMF: An Open-Source Data Mining Library*. <https://www.philippe-fournier-viger.com/spmf/>
- R. Agrawal et R. Srikant, « Fast Algorithms for Mining Association Rules », *Proc. 20th VLDB*, 1994.
- Documentation `mlxtend` : <https://rasbt.github.io/mlxtend/user_guide/frequent_patterns/apriori/>
- Documentation `arules` (R) : <https://cran.r-project.org/web/packages/arules/index.html>
- T. H. Cormen et al., *Introduction to Algorithms*, MIT Press, 4e éd. — chapitres sur DFS et BFS.
- Jeux de données SPMF : <https://www.philippe-fournier-viger.com/spmf/index.php?link=datasets.php>
