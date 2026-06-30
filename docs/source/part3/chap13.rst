.. _part3_chap13:

***********************************************************************
Chapitre 13 : gSpan
***********************************************************************

**Famille :** Graph Mining (motifs de **sous-graphes** fréquents).

Idée clé
========

**gSpan** (*graph-based Substructure pattern mining*) découvre les **sous-graphes
fréquents** dans une base de graphes, **sans génération de candidats**. Sa
contribution majeure : un **code canonique** des graphes, le **DFS code** (obtenu
par un parcours en profondeur), avec un **minimum DFS code** unique par graphe. En
n'étendant que les *minimum DFS codes* (*rightmost extension*), gSpan évite de
générer plusieurs fois le **même** sous-graphe (problème d'isomorphisme) et élague
efficacement, façon a priori.

Principe par l'exemple
======================

Chaque sous-graphe candidat est représenté par son DFS code ; on n'**étend** que le
long du *rightmost path* et on **ignore** tout code qui n'est pas minimal (donc
déjà exploré sous une autre forme). On élague dès qu'un sous-graphe n'est plus
fréquent (antimonotonicité).

Article de référence
=====================

Yan, X., & Han, J. (2002). *gSpan: Graph-Based Substructure Pattern Mining*. Proc.
IEEE Int'l Conf. on Data Mining (ICDM'02). — Implémentation :
`SPMF (gSpan) <https://www.philippe-fournier-viger.com/spmf/>`_.
