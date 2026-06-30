.. _part1_chap3:

***********************************************************************
Chapitre 3 : Algorithmes et complexité
***********************************************************************

Les algorithmes de data mining doivent **passer à l'échelle**. Ce chapitre rappelle
les notions d'efficacité et de complexité, et les parcours **DFS/BFS** — outils
indispensables pour explorer l'espace des motifs (:doc:`chap6 <../part2/chap6>`).

Objectifs
=========

À la fin de ce chapitre, vous devez pouvoir :

- Définir un algorithme et raisonner sur son **efficacité**
- Estimer une **complexité** (temps, mémoire) et comprendre la croissance exponentielle
- Implémenter et distinguer les parcours **DFS** et **BFS**


1. Algorithme et efficacité
===========================

Un **algorithme** est une suite finie d'instructions résolvant un problème. En data
mining, l'**efficacité** est cruciale : les espaces de recherche sont énormes
(p. ex. pour :math:`n` produits, il y a :math:`2^n` combinaisons possibles d'items).

On évalue un algorithme par sa **complexité** :

- **temporelle** (temps d'exécution selon la taille de l'entrée) ;
- **spatiale** (mémoire utilisée).

On l'exprime en notation **Grand O** (voir aussi le cours AQL). Une croissance
**exponentielle** (:math:`O(2^n)`) rend l'approche naïve impraticable : d'où le
besoin d'**heuristiques** et de **propriétés** (comme l'antimonotonicité, au
:doc:`chap6 <../part2/chap6>`) pour **élaguer** l'espace de recherche.


2. Recherche / exploration : DFS et BFS
=======================================

Explorer l'espace des candidats revient à parcourir un **arbre** (ou un treillis,
*lattice*) de possibilités. Deux parcours fondamentaux :

- **DFS** (*Depth-First Search*, parcours en profondeur) : on descend une branche
  jusqu'au bout avant de revenir en arrière (*backtracking*). Peu de mémoire.
- **BFS** (*Breadth-First Search*, parcours en largeur) : on explore niveau par
  niveau. Pratique pour exploiter les propriétés « par niveau » (a priori).

**DFS (récursif) — exploration de toutes les combinaisons d'un alphabet :**

.. code-block:: python

   def dfs(P, alphabet):
       print(P)                      # P est un candidat (un sous-ensemble)
       for e in alphabet:
           dfs(P + [e], alphabet)

   dfs([], alphabet)

Cette exploration naïve génère **toutes** les combinaisons. Pour la rendre
efficace, on ajoute un **ordre virtuel** sur les éléments (ne brancher que sur les
éléments *suivants*) puis on **élague** avec une propriété (a priori) — c'est
exactement la démarche du :doc:`Frequent Itemset Mining <../part2/chap6>`.


Exercice
========

1. Pour un alphabet de 4 éléments, combien de sous-ensembles l'exploration naïve génère-t-elle ?
2. Modifiez la fonction ``dfs`` pour ne brancher que sur les éléments **après** le dernier ajouté. Combien de candidats génère-t-elle alors ?
