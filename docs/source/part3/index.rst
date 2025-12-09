.. _part3:

*************************************************************************************************
Chapitre 3 | Fouille d'itemsets fréquents et règles d'association
*************************************************************************************************

Objectifs
=========

À l'issue de ce module, chaque étudiant.e sera capable de :

* Comprendre les concepts fondamentaux de la fouille d'itemsets fréquents (FIM)
* Expliquer et différencier les algorithmes Apriori, Eclat et FP-Growth
* Implémenter l'algorithme Apriori à partir de zéro
* Générer des règles d'association à partir d'itemsets fréquents
* Calculer et interpréter les mesures de qualité (support, confiance, lift)
* Comparer les performances des différents algorithmes sur des jeux de données variés
* Appliquer la fouille d'itemsets fréquents à des problèmes réels (analyse du panier de marché)
* Interpréter les résultats dans un contexte métier


Note de théorique
=======================================

Introduction à la fouille d'itemsets fréquents
"""""""""""""""""""""""""""""""""""""""""""""""

**Définitions de base :**

* **Item** : Un objet élémentaire (ex: un produit)
* **Itemset** : Un ensemble d'items (ex: {pain, beurre, lait})
* **Transaction** : Un ensemble d'items achetés ensemble
* **Base de données transactionnelle** : Collection de transactions

**Exemple de base de données transactionnelle :**

.. code-block:: text

   TID | Items
   ----|------------------
   T1  | {pain, lait}
   T2  | {pain, couches, bière, œufs}
   T3  | {lait, couches, bière, coca}
   T4  | {pain, lait, couches, bière}
   T5  | {pain, lait, couches, coca}

**Problème de la fouille d'itemsets fréquents :**

Étant donné :
- Une base de données transactionnelle D
- Un seuil de support minimum (min_sup)

Objectif : Trouver tous les itemsets qui apparaissent dans au moins min_sup transactions.

**Mesure de support :**

Le support d'un itemset X est le nombre (ou proportion) de transactions contenant X.

.. code-block:: text

   support(X) = |{T ∈ D | X ⊆ T}| / |D|

Exemple : support({pain, lait}) = 3/5 = 0.6 (60%)


Règles d'association
""""""""""""""""""""

**Définition :**

Une règle d'association est de la forme X → Y, où :
- X et Y sont des itemsets disjoints (X ∩ Y = ∅)
- X est l'antécédent (partie gauche)
- Y est le conséquent (partie droite)

**Mesures de qualité des règles :**

1. **Support** :
   
   .. code-block:: text
   
      support(X → Y) = support(X ∪ Y)
   
   Indique la fréquence de la règle dans la base de données.

2. **Confiance** :
   
   .. code-block:: text
   
      confidence(X → Y) = support(X ∪ Y) / support(X)
   
   Indique la probabilité de trouver Y sachant que X est présent.

3. **Lift** :
   
   .. code-block:: text
   
      lift(X → Y) = confidence(X → Y) / support(Y)
                  = support(X ∪ Y) / (support(X) × support(Y))
   
   Mesure l'indépendance entre X et Y :
   - lift > 1 : corrélation positive (X et Y apparaissent souvent ensemble)
   - lift = 1 : X et Y sont indépendants
   - lift < 1 : corrélation négative (X et Y apparaissent rarement ensemble)

**Exemple :**

Règle : {pain, beurre} → {lait}

- support = 0.3 (30% des transactions contiennent {pain, beurre, lait})
- confidence = 0.75 (75% des transactions avec {pain, beurre} contiennent aussi {lait})
- lift = 1.5 (les clients qui achètent {pain, beurre} ont 1.5 fois plus de chances d'acheter du lait)


Algorithme Apriori
""""""""""""""""""

**Principe de base :**

L'algorithme Apriori utilise la propriété d'anti-monotonie :
*Si un itemset est fréquent, alors tous ses sous-ensembles sont également fréquents.*

**Contraposée :**
*Si un itemset est infrequent, alors tous ses sur-ensembles sont également infrequents.*

**Étapes de l'algorithme :**

1. **Génération des candidats** :
   - k=1 : Scanner la base pour trouver les itemsets fréquents de taille 1
   - k>1 : Générer les candidats de taille k en combinant les itemsets fréquents de taille k-1

2. **Élagage** :
   - Supprimer les candidats dont un sous-ensemble de taille k-1 n'est pas fréquent

3. **Calcul du support** :
   - Scanner la base de données pour compter le support de chaque candidat

4. **Filtrage** :
   - Ne garder que les itemsets avec support ≥ min_sup

5. **Répéter** jusqu'à ce qu'aucun nouvel itemset fréquent ne soit trouvé

**Pseudo-code simplifié :**

.. code-block:: python

   def apriori(transactions, min_support):
       # Étape 1 : Trouver les itemsets de taille 1
       C1 = get_all_items(transactions)
       L1 = filter_by_support(C1, transactions, min_support)
       
       L = [L1]
       k = 2
       
       while L[k-2]:
           # Génération des candidats
           Ck = generate_candidates(L[k-2])
           
           # Calcul du support
           for transaction in transactions:
               for candidate in Ck:
                   if candidate.issubset(transaction):
                       candidate.count += 1
           
           # Filtrage
           Lk = [c for c in Ck if c.support >= min_support]
           L.append(Lk)
           k += 1
       
       return flatten(L)


Algorithme Eclat
""""""""""""""""

**Principe :**

Eclat (Equivalence Class Transformation) utilise une approche par intersection verticale :
- Représente les données sous forme verticale (item → liste de TIDs)
- Utilise l'intersection des listes de TIDs pour calculer le support

**Représentation verticale :**

.. code-block:: text

   Item  | TIDs
   ------|-------------
   pain  | {T1,T2,T4,T5}
   lait  | {T1,T3,T4,T5}
   bière | {T2,T3,T4}

**Avantage :** Pas besoin de scanner la base à chaque itération.

**Étapes :**

1. Transformer la base en format vertical
2. Générer des itemsets par intersection de TID-lists
3. Calculer le support en comptant la taille des intersections
4. Utiliser une approche de recherche en profondeur (DFS)


Algorithme FP-Growth
""""""""""""""""""""

**Principe :**

FP-Growth (Frequent Pattern Growth) évite la génération coûteuse de candidats en :
- Compressant la base dans une structure d'arbre (FP-tree)
- Minant l'arbre directement pour extraire les itemsets fréquents

**Structure FP-tree :**

- Chaque nœud représente un item
- Les chemins partagent des préfixes communs
- Une table d'en-tête pointe vers toutes les occurrences de chaque item

**Avantages :**

- Pas de génération de candidats
- Seulement 2 scans de la base de données
- Plus efficace qu'Apriori pour les grandes bases

**Étapes :**

1. Scanner la base pour trouver les items fréquents (1er scan)
2. Construire le FP-tree (2ème scan)
3. Miner récursivement le FP-tree pour extraire les patterns


Génération des règles d'association
""""""""""""""""""""""""""""""""""""

**À partir des itemsets fréquents :**

Pour chaque itemset fréquent l de taille ≥ 2 :
1. Générer tous les sous-ensembles non vides s de l
2. Pour chaque s, créer la règle s → (l - s)
3. Calculer la confiance de la règle
4. Ne garder que les règles avec confiance ≥ min_confidence

**Exemple :**

Itemset fréquent : {pain, beurre, lait} avec support = 0.3

Règles possibles :
- {pain} → {beurre, lait}
- {beurre} → {pain, lait}
- {lait} → {pain, beurre}
- {pain, beurre} → {lait}
- {pain, lait} → {beurre}
- {beurre, lait} → {pain}


Comparaison des algorithmes
""""""""""""""""""""""""""""

.. code-block:: text

   Algorithme | Approche          | Avantages                    | Inconvénients
   -----------|-------------------|------------------------------|------------------
   Apriori    | Largeur d'abord   | Simple, facile à comprendre  | Génère beaucoup de candidats
              | Horizontal        | Nombreuses implémentations   | Scans multiples de la base
   -----------|-------------------|------------------------------|------------------
   Eclat      | Profondeur        | Pas de scan répété           | Consommation mémoire
              | Vertical          | Rapide pour petites bases    | Gestion des TID-lists
   -----------|-------------------|------------------------------|------------------
   FP-Growth  | Compression       | Très efficace                | Structure complexe
              | Arbre             | Pas de génération candidats  | Difficile à implémenter
              |                   | Seulement 2 scans            |


Applications pratiques
""""""""""""""""""""""

**1. Analyse du panier de marché (Market Basket Analysis) :**
- Identifier les produits fréquemment achetés ensemble
- Optimiser le placement des produits en magasin
- Créer des promotions croisées

**2. Recommandation de produits :**
- Suggérer des produits complémentaires
- "Les clients qui ont acheté X ont aussi acheté Y"

**3. Détection de fraude :**
- Identifier des patterns inhabituels de transactions

**4. Analyse de logs web :**
- Comprendre les parcours utilisateurs
- Optimiser la navigation sur un site

**5. Analyse médicale :**
- Identifier les symptômes qui apparaissent ensemble
- Découvrir des effets secondaires de médicaments


Interprétation métier
"""""""""""""""""""""

**Bonnes pratiques :**

1. **Ne pas se fier uniquement au support** :
   - Un support élevé peut être banal (ex: tout le monde achète du pain)
   - Regarder aussi le lift et la confiance

2. **Attention au lift** :
   - lift > 1 : relation positive intéressante
   - Mais un lift élevé avec un support très faible peut être du bruit

3. **Filtrer les règles triviales** :
   - Ex: {pain, beurre, lait} → {lait} est redondante avec {pain, beurre} → {lait}

4. **Contexte métier essentiel** :
   - Une règle statistiquement forte peut être inutile en pratique
   - Ex: {couches} → {bière} célèbre mais controversée

5. **Validation par les experts** :
   - Les règles découvertes doivent être validées par des experts métier
   - Certaines peuvent être surprenantes mais explicables


À lire / Aller plus loin
=======================================

**Slides du cours :** [À compléter]


**Livres de référence :**


**Ressources en ligne :**

- UCI Machine Learning Repository : datasets pour FIM
- SPMF Library : implémentations d'algorithmes de pattern mining
- mlxtend (Python) : implémentation d'Apriori et règles d'association

**Aller plus loin :**




Exercices théoriques
=======================================

.. note::
   Vous devez faire ces exercices avant la prochaine séance.

Exercice 1 - Calcul manuel
""""""""""""""""""""""""""

Soit la base de données transactionnelle suivante :

.. code-block:: text

   TID | Items
   ----|------------------
   T1  | {A, B, C}
   T2  | {A, B, D}
   T3  | {A, C, D}
   T4  | {B, C, D}
   T5  | {A, B, C, D}

Avec min_support = 60% (3 transactions) :

1. Listez tous les itemsets fréquents de taille 1, 2, et 3
2. Calculez le support de chaque itemset
3. Générez toutes les règles d'association avec min_confidence = 70%
4. Pour chaque règle, calculez le support, la confiance et le lift
5. Identifiez les 3 règles les plus intéressantes selon le lift


Exercice 2 - Analyse critique de règles
""""""""""""""""""""""""""""""""""""""""

Une chaîne de supermarchés découvre les règles suivantes :

1. {pain} → {lait} : support=0.15, confidence=0.80, lift=1.3
2. {couches} → {bière} : support=0.02, confidence=0.85, lift=3.5
3. {pâtes, sauce tomate} → {parmesan} : support=0.08, confidence=0.75, lift=2.1
4. {eau} → {pain} : support=0.25, confidence=0.60, lift=1.1

Pour chaque règle :
- Interprétez les mesures (support, confidence, lift)
- Évaluez l'intérêt métier de la règle
- Proposez une action marketing concrète si la règle est intéressante
- Justifiez pourquoi certaines règles pourraient être écartées



Exercice pratique (TP2)
=======================================

.. note::
   Ce TP constitue une partie importante de votre évaluation (TP2).

Partie 1 : Implémentation de l'algorithme Apriori
""""""""""""""""""""""""""""""""""""""""""""""""""

**Objectif :** Implémenter l'algorithme Apriori from scratch en Python.

**Spécifications techniques :**

1. **Fonctions à implémenter** :
   
   .. code-block:: python
   
      def load_transactions(filename):
          """Charge les transactions depuis un fichier"""
          pass
      
      def calculate_support(itemset, transactions):
          """Calcule le support d'un itemset"""
          pass
      
      def generate_candidates(prev_frequent, k):
          """Génère les candidats de taille k"""
          pass
      
      def prune_candidates(candidates, prev_frequent):
          """Élague les candidats en utilisant la propriété d'anti-monotonie"""
          pass
      
      def apriori(transactions, min_support):
          """Algorithme Apriori complet"""
          pass
      
      def generate_rules(frequent_itemsets, transactions, min_confidence):
          """Génère les règles d'association"""
          pass
      
      def calculate_metrics(rule, transactions):
          """Calcule support, confidence, lift pour une règle"""
          pass

2. **Datasets à utiliser** :
   - Dataset "Online Retail" (UCI)
   - Dataset "Market Basket Optimization" (Kaggle)

3. **Livrables** :
   - Code Python bien commenté et structuré
   - Tests unitaires pour chaque fonction clé
   - Fichier README expliquant comment utiliser votre implémentation


Partie 2 : Analyse comparative des algorithmes
"""""""""""""""""""""""""""""""""""""""""""""""

**Objectif :** Comparer Apriori (votre implémentation) avec Eclat et FP-Growth (bibliothèques).

**Tâches :**

1. **Implémentations à comparer** :
   - Votre Apriori
   - Eclat (depuis mlxtend ou pyfim)
   - FP-Growth (depuis mlxtend)

2. **Métriques de comparaison** :
   - Temps d'exécution
   - Nombre d'itemsets fréquents trouvés
   - Utilisation mémoire (optionnel)
   - Scalabilité avec différents seuils de support

3. **Expériences à mener** :
   - Varier le min_support : 0.01, 0.05, 0.1, 0.2
   - Utiliser différents datasets de tailles variées
   - Créer des graphiques comparatifs

4. **Livrables** :
   - Script de benchmarking
   - Graphiques de comparaison (temps vs support, itemsets vs support)
   - Analyse écrite des résultats (2-3 pages)
