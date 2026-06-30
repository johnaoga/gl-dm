.. _part4:

*************************************************************************************************
Partie 4 | Projet & TPs
*************************************************************************************************

Cette partie regroupe les **travaux pratiques** (implémentation) et le **projet**
final. Selon votre :doc:`parcours <../parcours/pigier>`, tous les TP ne sont pas au
programme (voir le tableau ci-dessous).

.. list-table:: TP par parcours
   :header-rows: 1
   :widths: 46 27 27

   * - TP
     - PIGIER
     - IFRI
   * - TP — Analyse descriptive
     - ✅
     - ✗
   * - TP — Implémentation de K-means
     - ✅
     - ✅
   * - TP — Implémentation d'Apriori
     - ✅
     - ✅
   * - TP — Implémentation de PrefixSpan
     - ✗
     - ✅
   * - Projet final
     - ✅
     - ✅


TP — Analyse descriptive
========================

Récupérer le **jeu de données assigné** et en faire toute l'analyse descriptive :

1. Identifier un **sujet d'orientation** ;
2. **Analyser** les données (univariée + multivariée) ;
3. **Interpréter** les courbes à la lumière du sujet ;
4. Tirer une **conclusion** de base.

*Exemples de datasets (R) :* ``iris``, ``mtcars``, ``Titanic``, ``quakes``,
``airquality``, ``trees``, ``ToothGrowth``, … (un par étudiant).


TP — Implémentation de K-means
==============================

1. Implémenter K-means (Python ou autre) sur le jeu assigné ;
2. Faire plusieurs essais et déterminer le **K optimal** (méthode du coude) ;
3. Déterminer les K groupes pour ce K.


TP — Implémentation d'Apriori
=============================

Écrire un programme qui reçoit une **base de transactions** et un :math:`\theta`, et
renvoie la liste des **patterns fréquents**.

*Indices (Python) :* ``itertools``, ``set.issubset`` / ``set.issuperset``.


TP — Implémentation de PrefixSpan *(IFRI)*
==========================================

Implémenter le *sequential pattern mining* (PrefixSpan), **vérifier** qu'on obtient
les mêmes patterns qu'une implémentation connue, puis **comparer les performances** à
une implémentation de référence (SPMF) et à **LCM** :

- faire varier le support entre **20 % et 1 %** (≤ 10 points, *timeout* 30 min) ;
- même environnement d'exécution pour tous ;
- tracer le **temps d'exécution** (y) en fonction du support (x) pour chaque dataset
  (votre algo, l'original et LCM sur la même courbe).

*Datasets de référence :* `FIMI <http://fimi.uantwerpen.be/data/>`_ (mushroom, chess,
connect, retail…), SPMF (Fifa, Bible, Online Retail…).


Projet final — Data Mining
==========================

**Objectif :** appliquer une démarche complète de data mining à un **jeu de données
réel**, centrée sur le *frequent itemset mining* et le *sequential pattern mining* —
identifier des patterns (et règles d'association si pertinent) et en tirer des
**insights actionnables**.

**Étapes (CRISP-DM) :** collecte des données → prétraitement → modélisation (FIM :
Apriori/Eclat/FP-Growth ; SPM : PrefixSpan/CloSpan ; règles d'association) → analyse
→ restitution (visualisations + rapport).

**Livrables :**

- un **rapport** (10 pages hors intro/conclusion/références) : collecte,
  prétraitement, techniques de fouille, résultats, analyse, **recommandations** ;
- des **visualisations** (heatmaps, bar charts, diagrammes de séquences…) ;
- le **code**.

**Thèmes proposés :**

1. **Customer Behavior Analysis** (télécommunications) — combinaisons de services, séquences d'adoption ; rétention.
2. **Tourist Habits Analysis** — combinaisons d'activités, itinéraires ; packages touristiques.
3. **Pattern Mining in Text Data** — patterns fermés/maximaux de mots ; thèmes clés.
4. **Transportation / Traffic Management** — routes fréquentes, séquences de trajets ; fluidification.
5. **COVID-19 Pattern Analysis** (santé publique) — clusters de symptômes, progression ; allocation de ressources.

.. note::
   Les **dates de rendu**, l'**attribution** des datasets/thèmes et les **poids**
   d'évaluation sont communiqués par l'enseignant.
