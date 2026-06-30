.. _part3_chap10:

***********************************************************************
Chapitre 10 : PrefixSpan
***********************************************************************

**Famille :** Sequential Pattern Mining (SPM).

Idée clé
========

**PrefixSpan** (*Prefix-projected Sequential pattern mining*) découvre les
séquences fréquentes par **croissance de préfixes** (*pattern-growth*), **sans
génération de candidats**. À chaque préfixe fréquent, il construit une **base de
données projetée** (les **suffixes** des séquences qui contiennent le préfixe), puis
récursivement étend le préfixe avec les items fréquents de la projection. Les
**bases pseudo-projetées** (pointeurs plutôt que copies) évitent de dupliquer les
données.

Principe par l'exemple
======================

1. Trouver les items fréquents (séquences de longueur 1) → préfixes initiaux.
2. Pour un préfixe :math:`\langle a \rangle`, projeter la SDB : pour chaque séquence
   contenant *a*, ne garder que le **suffixe** après le premier *a*.
3. Compter les items fréquents dans la base projetée → étendre le préfixe
   (:math:`\langle a, b \rangle`, …) et **recurser** sur la nouvelle projection.

La taille des projections **décroît** à mesure que les préfixes s'allongent, d'où
l'efficacité.

Article de référence
=====================

Pei, J., Han, J., Mortazavi-Asl, B., Wang, J., Pinto, H., Chen, Q., Dayal, U., &
Hsu, M.-C. (2004). *Mining Sequential Patterns by Pattern-Growth: The PrefixSpan
Approach*. IEEE Trans. Knowledge and Data Engineering, 16(11), 1424–1440. —
Implémentation : `SPMF (PrefixSpan) <https://www.philippe-fournier-viger.com/spmf/>`_.
