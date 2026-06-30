.. _part3_chap8:

***********************************************************************
Chapitre 8 : Eclat
***********************************************************************

**Famille :** Frequent Itemset Mining (FIM).

Idée clé
========

**Eclat** explore le treillis en **profondeur (DFS)** avec la propriété **a priori**,
mais calcule le support **sans rebalayer la base** : il utilise une **TDB
verticale** (à chaque item on associe sa *tid-list* = l'ensemble des transactions
qui le contiennent) et obtient le support d'une combinaison par **intersection** de
tid-lists.

.. math::

   \text{tidlist}(X \cup Y) = \text{tidlist}(X) \cap \text{tidlist}(Y)
   \qquad \text{support}(X) = |\text{tidlist}(X)|

La variante **dEclat** remplace les tid-lists par des **diffsets** (différences
d'ensembles), bien plus compacts sur les bases denses.

Principe par l'exemple
======================

Format vertical : ``a → {1,2,3,5}``, ``b → {1,2,3}``, ``c → {2,3,5}``. Alors
``{a,b}`` a pour tid-list ``{1,2,3} = {1,2,3,5} ∩ {1,2,3}`` ⇒ support 3. On
descend en DFS et on **élague** dès que le support passe sous :math:`\theta`.

Article de référence
=====================

Zaki, M. J., & Gouda, K. (2001). *Fast vertical mining using diffsets*. Technical
Report 01-1, Computer Science Dept., Rensselaer Polytechnic Institute. — Implémentation :
`SPMF (dEclat/dCharm) <https://www.philippe-fournier-viger.com/spmf/>`_.
