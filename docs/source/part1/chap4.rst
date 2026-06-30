.. _part1_chap4:

***********************************************************************
Chapitre 4 : Outils d'implémentation
***********************************************************************

Pour mettre en pratique le data mining, on dispose de trois familles d'outils :
**langages de programmation**, **solveurs/bibliothèques spécialisés** et
**logiciels tout-en-un**.

Objectifs
=========

À la fin de ce chapitre, vous devez pouvoir :

- Choisir un **langage** adapté à une tâche de data mining
- Citer des **bibliothèques/solveurs** spécialisés et des **logiciels** tout-en-un
- Savoir où trouver des **implémentations de référence**


1. Langages de programmation
============================

- **Python** — l'écosystème de référence en data science : ``pandas`` (manipulation),
  ``numpy`` (calcul), ``scikit-learn`` (ML/clustering), ``matplotlib``/``seaborn``
  (visualisation), ``mlxtend`` (Apriori/FP-Growth).
- **R** — très présent en statistique et exploration : ``dplyr``/``tidyverse``,
  ``ggplot2``, ``arules`` (règles d'association).

.. note::
   Les bases de Python et de R sont traitées dans le cours
   `PythonR <https://johnaoga.github.io/pythonr/>`_ — réutilisez-le comme référence.


2. Solveurs et bibliothèques spécialisés
========================================

Pour le *pattern mining*, des bibliothèques fournissent des implémentations
optimisées (souvent plus rapides qu'une implémentation maison) :

- **SPMF** (`spmf <https://www.philippe-fournier-viger.com/spmf/>`_) — une référence
  Java : Apriori, Eclat, FP-Growth, PrefixSpan, CloSpan, CMRules, gSpan, … (utilisée
  en :doc:`Partie 3 <../part3/index>`).
- **FIMI** (`fimi <http://fimi.uantwerpen.be/>`_) — implémentations et **datasets**
  de référence pour le *frequent itemset mining* (dont **LCM**, parmi les plus rapides).


3. Logiciels tout-en-un (standalone)
====================================

Des environnements graphiques permettent de faire du data mining **sans coder** (par
glisser-déposer de blocs) :

- **Weka**, **Orange**, **KNIME**, **RapidMiner** — exploration, prétraitement,
  clustering, règles d'association, évaluation.

Ils sont utiles pour prototyper vite et pour enseigner les concepts, avant de passer
à une implémentation programmée pour plus de contrôle et de performance.


Exercice
========

Pour chacune de ces tâches, proposez **un** outil adapté et justifiez : (a) analyse
descriptive rapide d'un CSV ; (b) Apriori sur une grosse base de transactions ;
(c) prototypage visuel d'un *pipeline* de clustering sans coder.
