.. _part4:

*************************************************************************************************
Chapitre 4 | Fouille de motifs séquentiels
*************************************************************************************************

Objectifs
=========

À l'issue de ce module, chaque étudiant.e sera capable de :

* Comprendre les concepts fondamentaux de la fouille de motifs séquentiels (SPM)
* Différencier les itemsets fréquents des séquences fréquentes
* Expliquer et implémenter l'algorithme PrefixSpan
* Identifier des patterns temporels dans des données transactionnelles
* Appliquer la fouille de séquences sur des données réelles (parcours utilisateurs, logs, etc.)
* Comparer PrefixSpan avec d'autres approches (CloSpan)
* Interpréter les séquences fréquentes dans un contexte applicatif


Note de théorique
=======================================

Introduction à la fouille de motifs séquentiels
""""""""""""""""""""""""""""""""""""""""""""""""

**Différence avec les itemsets fréquents :**

* **Itemsets** : Ensembles d'items sans ordre (ex: {pain, lait, beurre})
* **Séquences** : Listes ordonnées d'itemsets (ex: <{pain}, {lait, beurre}, {œufs}>)

**Définitions de base :**

* **Événement (itemset)** : Ensemble d'items se produisant au même moment
* **Séquence** : Liste ordonnée d'événements notée s = <e₁, e₂, ..., eₙ>
* **Longueur d'une séquence** : Nombre total d'items dans la séquence
* **k-séquence** : Séquence contenant k items
* **Base de données séquentielle** : Collection de séquences, chaque séquence associée à un identifiant

**Exemple de base de données séquentielle :**

.. code-block:: text

   SID | Séquence
   ----|-----------------------------------------------
   S1  | <{a}, {a,b,c}, {a,c}, {d}, {c,f}>
   S2  | <{a,d}, {c}, {b,c}, {a,e}>
   S3  | <{e,f}, {a,b}, {d,f}, {c}, {b}>
   S4  | <{e}, {g}, {a,f}, {c}, {b}, {c}>

**Exemple concret - parcours web :**

.. code-block:: text

   UserID | Parcours de pages
   -------|-----------------------------------------------
   U1     | <{Accueil}, {Produits}, {Panier}, {Paiement}>
   U2     | <{Accueil}, {Blog}, {Produits}, {Contact}>
   U3     | <{Accueil}, {Produits}, {Détails}, {Panier}>


Sous-séquence et support
"""""""""""""""""""""""""

**Relation de sous-séquence :**

Une séquence s = <a₁, a₂, ..., aₙ> est une sous-séquence de s' = <b₁, b₂, ..., bₘ> (noté s ⊑ s') s'il existe des entiers 1 ≤ j₁ < j₂ < ... < jₙ ≤ m tels que :
* a₁ ⊆ bⱼ₁
* a₂ ⊆ bⱼ₂
* ...
* aₙ ⊆ bⱼₙ

**Exemples :**

.. code-block:: text

   Séquence s' = <{a}, {b,c}, {d}, {e}>
   
   Sous-séquences valides :
   - <{a}, {b}> ⊑ s'
   - <{a}, {c}, {e}> ⊑ s'
   - <{b,c}, {d}> ⊑ s'
   - <{a}, {d}> ⊑ s'
   
   Non sous-séquences :
   - <{b}, {a}> ⊄ s' (ordre inversé)
   - <{b,c,d}> ⊄ s' (pas dans le même événement)

**Support d'une séquence :**

Le support d'une séquence s est le nombre (ou proportion) de séquences de la base qui contiennent s comme sous-séquence.

.. code-block:: text

   support(s) = |{sid | s ⊑ S_sid}| / |DB|

**Séquence fréquente :**

Une séquence est fréquente si son support ≥ min_support.

**Problème de la fouille de séquences :**

Étant donné :
- Une base de données séquentielle DB
- Un seuil de support minimum min_sup

Objectif : Trouver toutes les séquences fréquentes.


Propriétés importantes
""""""""""""""""""""""

**1. Propriété d'anti-monotonie (Apriori) :**

Si une séquence s est fréquente, alors toutes ses sous-séquences sont également fréquentes.

**Contraposée :**
Si une séquence est infrequente, alors toutes ses super-séquences sont également infrequentes.

**2. Propriété de projection :**

Le support d'une séquence dans une base projetée est identique à son support dans la base originale (pour les séquences ayant un préfixe donné).

Cette propriété est fondamentale pour l'algorithme PrefixSpan.


Algorithme PrefixSpan 
""""""""""""""""""""""""""

**Principe :**

PrefixSpan évite la génération coûteuse de candidats en :
1. Utilisant une approche récursive pattern-growth
2. Divisant l'espace de recherche par projection de bases
3. Exploitant uniquement les séquences prometteuses

**Concepts clés :**

* **Préfixe** : Sous-séquence contiguë du début d'une séquence
  
  .. code-block:: text
  
     Séquence : <{a}, {b,c}, {d}>
     Préfixes : <>, <{a}>, <{a}, {b}>, <{a}, {b,c}>, <{a}, {b,c}, {d}>

* **Suffixe** : Ce qui reste après avoir retiré le préfixe
  
  .. code-block:: text
  
     Séquence : <{a}, {b,c}, {d}>
     Préfixe : <{a}, {b}>
     Suffixe : <(_c), {d}> où _c signifie que c reste dans le même événement

* **Base projetée** : Ensemble des suffixes des séquences ayant un préfixe donné

**Étapes de l'algorithme PrefixSpan :**

1. **Initialisation** :
   - Scanner la base pour trouver les 1-séquences fréquentes
   - Ce sont les préfixes de longueur 1

2. **Récursion** :
   Pour chaque préfixe fréquent α :
   
   a. Construire la base projetée α-projetée
   b. Miner récursivement α-projetée pour trouver les séquences fréquentes commençant par α
   c. Les préfixes trouvés deviennent de nouveaux préfixes à explorer

3. **Arrêt** :
   - Quand la base projetée est vide
   - Ou quand aucune séquence fréquente n'est trouvée

**Exemple détaillé :**

Base de données :

.. code-block:: text

   SID | Séquence
   ----|------------------------
   S1  | <{a}, {a,b,c}, {a,c}, {d}, {c,f}>
   S2  | <{a,d}, {c}, {b,c}, {a,e}>
   S3  | <{e,f}, {a,b}, {d,f}, {c}, {b}>
   S4  | <{e}, {g}, {a,f}, {c}, {b}, {c}>

Avec min_support = 50% (2 séquences)

**Étape 1 - Trouver les 1-séquences fréquentes :**

.. code-block:: text

   Item | Support | Fréquent?
   -----|---------|----------
   <{a}>|   4/4   |    Oui
   <{b}>|   4/4   |    Oui
   <{c}>|   4/4   |    Oui
   <{d}>|   3/4   |    Oui
   <{e}>|   3/4   |    Oui
   <{f}>|   3/4   |    Oui
   <{g}>|   1/4   |    Non

**Étape 2 - Explorer le préfixe <{a}> :**

Construire la base <{a}>-projetée :

.. code-block:: text

   SID | <{a}>-Suffixe projeté
   ----|------------------------
   S1  | <(_b,_c), {a,c}, {d}, {c,f}>
   S2  | <(_d), {c}, {b,c}, {a,e}>
   S3  | <(_b), {d,f}, {c}, {b}>
   S4  | <(_f), {c}, {b}, {c}>

Note : _x signifie que x est dans le même événement que 'a'

Trouver les items fréquents dans cette base projetée :

.. code-block:: text

   Item    | Support | Séquences fréquentes trouvées
   --------|---------|------------------------------
   <{a,b}> |   2/4   | <{a,b}> est fréquent (via _b)
   <{a,c}> |   2/4   | <{a,c}> est fréquent (via _c)
   <{a,d}> |   2/4   | <{a,d}> est fréquent (via _d)
   <{a},{c}>| 4/4    | <{a},{c}> est fréquent

**Étape 3 - Explorer récursivement <{a},{c}> :**

Construire la base <{a},{c}>-projetée et continuer...

**Avantages de PrefixSpan :**

- Pas de génération de candidats
- Un seul scan initial de la base
- Bases projetées généralement petites
- Approche divide-and-conquer efficace
- Meilleure performance que GSP sur grandes bases

**Complexité :**

- Temps : O(n × 2^m) dans le pire cas (n = taille DB, m = longueur max séquence)
- Pratique : Beaucoup plus efficace grâce aux projections


Comparaison avec d'autres approches
""""""""""""""""""""""""""""""""""""

**Algorithme CloSpan (Closed Sequential Pattern Mining) :**

Une séquence s est fermée si :
- Elle est fréquente
- Aucune de ses super-séquences n'a le même support

Avantages des séquences fermées :
- Ensemble plus compact (moins de patterns)
- Contient la même information que toutes les séquences fréquentes
- Réduit la redondance

**Comparaison synthétique :**

.. code-block:: text

Algorithme | Approche         | Avantages                  | Inconvénients
-----------|------------------|----------------------------|------------------
PrefixSpan | Pattern-growth   | Pas de candidats           | Récursion profonde
           | Projection       | Un seul scan initial       | Implémentation complexe
           |                  | Très efficace              |
-----------|------------------|----------------------------|------------------
CloSpan    | Closed patterns  | Résultats compacts         | Vérification fermeture
           | Pattern-growth   | Moins de redondance        | Plus complexe





À lire / Aller plus loin
=======================================

**Slides du cours :** [À compléter]



**Livres de référence :**


**Ressources en ligne :**

- SPMF Library : http://www.philippe-fournier-viger.com/spmf/
  * Implémentations de PrefixSpan, CloSpan
- Datasets pour SPM :
  * MSNBC Web Navigation : http://kdd.ics.uci.edu/databases/msnbc/msnbc.html
  * Kosarak : http://fimi.uantwerpen.be/data/


**Aller plus loin :**




Exercices théoriques
=======================================

.. note::
   Vous devez faire ces exercices avant la prochaine séance.


Exercice 1 - Calcul de support
"""""""""""""""""""""""""""""""

Soit la base de données séquentielle suivante :

.. code-block:: text

   SID | Séquence
   ----|------------------------
   S1  | <{a}, {b}, {c}, {d}>
   S2  | <{a}, {c}, {b}, {d}>
   S3  | <{a}, {b}, {d}>
   S4  | <{b}, {c}, {d}>
   S5  | <{a}, {b}, {c}>

Avec min_support = 60% (3 séquences) :

1. Calculez le support de chacune des séquences suivantes :
   - <{a}>
   - <{a}, {b}>
   - <{a}, {c}>
   - <{a}, {b}, {c}>
   - <{a}, {b}, {d}>
   - <{b}, {c}>
   - <{b}, {d}>

2. Quelles sont les séquences fréquentes de longueur 2 ?
3. Quelles sont les séquences fréquentes de longueur 3 ?





Exercice pratique (TP2bis)
=======================================

.. note::
   Ce TP constitue une partie de votre évaluation (TP2bis).

Partie 1 : Implémentation de PrefixSpan
""""""""""""""""""""""""""""""""""""""""

**Objectif :** Implémenter et appliquer PrefixSpan pour analyser des données séquentielles réelles.

Partie 2 : Comparaison avec CloSpan 
"""""""""""""""""""""""""""""""""""""""""

**Objectif :** Comparer les résultats de PrefixSpan avec les séquences fermées (CloSpan).

**Tâches :**

1. Utiliser SPMF Library
2. Comparer :
   - Nombre de séquences totales vs fermées
   - Taux de compression
   - Perte d'information (si applicable)

