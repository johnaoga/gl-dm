.. _part2_chap7:

***********************************************************************
Chapitre 7 : Sequential Pattern Mining
***********************************************************************

Le **Sequential Pattern Mining** (SPM) étend le FIM en **prenant en compte
l'ordre** : on cherche les **séquences** fréquentes.

Objectifs
=========

À la fin de ce chapitre, vous devez pouvoir :

- Distinguer un *itemset* (ensemble) d'une *séquence* (ordonnée)
- Formaliser le problème de SPM
- Comprendre pourquoi l'approche naïve échoue et comment GSP/a priori s'appliquent


1. Motivation
=============

Beaucoup de problèmes sont **cadencés par le temps** : il y a un **ordre** dans
l'arrivée des événements (ex. le circuit linéaire le plus fréquenté par les
touristes, l'ordre d'achat des produits, la progression de symptômes…).

On considère le panier **avec l'ordre** de choix ⇒ ce sont des **séquences** ⇒ on
fait du **Frequent Sequence Mining**. L'ensemble des séquences forme la base de
séquences **SDB** (*Sequence Database*).


2. Le problème de SPM
=====================

Soit un alphabet :math:`\Sigma`, une séquence :math:`S` (l'ordre compte), une base
:math:`SDB` et un seuil :math:`\theta`.

.. math::

   \text{support}(P, SDB) = |\{\, sid_i : S_i \in SDB \ \text{et}\ P \sqsubseteq S_i \,\}|

.. admonition:: Problème de SPM
   :class: important

   Trouver **toutes** les séquences :math:`P` telles que
   :math:`\text{support}(P, SDB) \ge \theta`.


3. Résolution
=============

Différence majeure avec le FIM : on **ne peut plus** construire un treillis fini —
il y a une **infinité** de combinaisons possibles (une séquence peut répéter des
items, être arbitrairement longue).

- **Approche naïve** : ne marche pas (espace infini).
- **Approche heuristique** : borner par la **longueur de la plus longue séquence**,
  considérer d'abord les items au support le plus fort.
- **Approche a priori** : heuristique + **antimonotonicité** ⇒ **GSP**
  (*Generalized Sequential Pattern*).
- **Approches avancées** (:doc:`Partie 3 <../part3/index>`) :

  - **PrefixSpan** : a priori + **bases de données projetées** (et pseudo-projetées) ;
  - **SPADE** : SDB **verticale** + intersection des *covers* par calcul binaire efficace ;
  - **CloSpan** : séquences **fermées** (compression sans perte).


4. Clarifications
=================

- **Fréquence** et **support** désignent généralement la même chose (le nombre
  d'occurrences). Nuance : *support* = support **absolu** (un nombre) ; *fréquence*
  = support **relatif** (proportion).
- En SPM, on appelle parfois *fréquence* le support des **singletons**.


Exercice
========

Voir le :doc:`TP PrefixSpan <../part4/index>` (parcours IFRI) : implémentez le
*sequential pattern mining* et comparez vos résultats à une implémentation de
référence (SPMF).
