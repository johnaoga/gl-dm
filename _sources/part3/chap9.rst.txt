.. _part3_chap9:

***********************************************************************
Chapitre 9 : FP-Growth
***********************************************************************

**Famille :** Frequent Itemset Mining (FIM).

Idée clé
========

**FP-Growth** (*Frequent-Pattern Growth*) extrait les itemsets fréquents **sans
génération de candidats**. Il compresse la base (TDB **horizontale**) dans un
**FP-tree** (arbre préfixe des transactions, items triés par support décroissant),
puis explore récursivement des **bases conditionnelles** (*conditional FP-trees*)
pour faire « croître » les patterns. On évite ainsi les coûteux balayages répétés
d'Apriori.

Principe par l'exemple
======================

1. Compter le support de chaque item, **ordonner** par support décroissant, élaguer
   les items sous :math:`\theta`.
2. Insérer chaque transaction (triée) dans le **FP-tree** en partageant les préfixes
   communs (chemins fusionnés + compteurs).
3. Pour chaque item (du moins au plus fréquent), construire sa **base conditionnelle**
   (les chemins qui y mènent) et son FP-tree conditionnel, puis récursivement en
   déduire les patterns fréquents.

Article de référence
=====================

Han, J., Pei, J., Yin, Y., & Mao, R. (2004). *Mining Frequent Patterns without
Candidate Generation: A Frequent-Pattern Tree Approach*. Data Mining and Knowledge
Discovery, 8(1), 53–87. — Implémentation :
`SPMF (FPGrowth) <https://www.philippe-fournier-viger.com/spmf/>`_.
