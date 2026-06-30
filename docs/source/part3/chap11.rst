.. _part3_chap11:

***********************************************************************
Chapitre 11 : CloSpan
***********************************************************************

**Famille :** Sequential Pattern Mining (SPM), motifs **fermés**.

Idée clé
========

**CloSpan** (*Closed Sequential pattern mining*) ne renvoie que les séquences
**fermées** — celles qui n'ont **pas** de sur-séquence de **même support**. C'est une
**compression sans perte** de l'ensemble des séquences fréquentes (souvent énorme).
Il s'appuie sur la croissance de préfixes (façon PrefixSpan) mais **élague** tôt
grâce à la détection d'**équivalences** de bases projetées (techniques de
*backward sub-pattern* et *backward super-pattern*), évitant d'explorer des branches
qui produiraient des séquences non fermées.

Principe par l'exemple
======================

Si :math:`\langle a, b \rangle` et :math:`\langle a, b, c \rangle` ont le **même
support**, alors :math:`\langle a, b \rangle` n'est **pas** fermée : seule la plus
longue est conservée. CloSpan repère ces situations (bases projetées identiques) et
**fusionne/élague** les recherches redondantes.

Article de référence
=====================

Yan, X., Han, J., & Afshar, R. (2003). *CloSpan: Mining Closed Sequential Patterns
in Large Datasets*. Proc. 2003 SIAM Int'l Conf. on Data Mining (SDM'03), 166–177. —
Implémentation : `SPMF (CloSpan) <https://www.philippe-fournier-viger.com/spmf/>`_.
