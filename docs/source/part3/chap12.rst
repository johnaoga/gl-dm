.. _part3_chap12:

***********************************************************************
Chapitre 12 : CMRules
***********************************************************************

**Famille :** Sequential Rule Mining (règles **séquentielles**).

Idée clé
========

**CMRules** découvre des **règles séquentielles communes à plusieurs séquences** :
des règles :math:`X \Rightarrow Y` signifiant que si les items :math:`X`
apparaissent, alors les items :math:`Y` apparaissent **plus tard** dans la même
séquence — mesurées par **support** et **confiance** (à l'échelle de l'ensemble des
séquences, pas d'une seule). L'idée : d'abord extraire des **règles d'association**
classiques (rapide), puis **vérifier/filtrer** lesquelles respectent la contrainte
d'**ordre** séquentiel — ce qui élague fortement l'espace de recherche.

Principe par l'exemple
======================

Sur des séquences d'achats, une règle :math:`\{pain\} \Rightarrow \{beurre\}`
indique que les clients qui achètent du pain achètent **ensuite** du beurre, dans
une proportion suffisante de séquences (support) et avec une confiance suffisante.

Article de référence
=====================

Fournier-Viger, P., Faghihi, U., Nkambou, R., & Mephu Nguifo, E. (2012). *CMRules:
Mining Sequential Rules Common to Several Sequences*. Knowledge-Based Systems,
25(1), 63–76 (Elsevier). — Implémentation :
`SPMF (CMRules) <https://www.philippe-fournier-viger.com/spmf/>`_.
