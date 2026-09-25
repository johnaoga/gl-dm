.. _intro:

*********************
Organisation du cours
*********************

Le **Data Mining** (fouille de données) consiste à **découvrir des connaissances
ou informations utiles, souvent cachées**, dans les données — afin d'appuyer la
**prise de décision**. Ce cours en couvre les concepts, les algorithmes (de base
et avancés) et leur mise en pratique.

À la fin de ce cours, l'étudiant.e doit être capable de :

* Différencier les notions autour de l'IA et de la donnée (IA, science des données, ML, DL, visualisation, *preprocessing*)
* Expliquer le processus **CRISP-DM**
* Réaliser l'**analyse descriptive** d'un jeu de données
* Identifier les éléments du data mining et leurs domaines d'application
* Identifier les **cas d'usage** des algorithmes de DM
* Implémenter des algorithmes de DM (*frequent itemset mining*, clustering, …)
* *(IFRI)* Lire et expliquer un **article scientifique** en data mining, et présenter un algorithme avancé


Pédagogie
=======================================

La pédagogie est mixte et basée sur l'**apprentissage par projet**. Nous alternerons
cours magistraux, travaux pratiques d'implémentation, analyses descriptives,
exposés (pour l'IFRI : présentation d'algorithmes avancés à partir de papiers) et
un projet final appliquant une démarche complète (CRISP-DM).


Deux entités, deux parcours
=======================================

Ce cours est enseigné dans **deux entités**. Le **contenu (chapitres) est commun**,
mais chaque entité suit un **parcours** différent (objectif, agenda, évaluation,
sélection de chapitres) :

.. list-table::
   :header-rows: 1
   :widths: 22 39 39

   * -
     - :doc:`PIGIER <../parcours/pigier>` (M2)
     - :doc:`IFRI <../parcours/ifri>` (M2)
   * - **Orientation**
     - Fondamentaux du data mining
     - Approfondi : algorithmes avancés issus d'articles scientifiques
   * - **Volume**
     - 7 séances × 3 h
     - 10 séances × 4 h
   * - **Parties couvertes**
     - Parties 1–2 (sélection) + Partie 4
     - Parties 1–3 (Parties 1–2 plus rapidement) + Partie 4
   * - **Spécificités**
     - TP analyse descriptive ; pas d'algorithmes avancés
     - Pas de TP analyse descriptive ; **exposés d'algorithmes avancés** (Partie 3) + lecture de papiers + projet final


Répartition du contenu
=======================================

* :doc:`Partie 1 <../part1/index>` — **Préliminaires** : introduction au data mining, analyse de données, algorithmes & complexité, outils.
* :doc:`Partie 2 <../part2/index>` — **Algorithmes de base** : clustering, *frequent itemset mining* (+ règles d'association), *sequential pattern mining*.
* :doc:`Partie 3 <../part3/index>` — **Algorithmes avancés** (IFRI, exposés) : Eclat, FP-Growth, PrefixSpan, CloSpan, CMRules, gSpan.
* :doc:`Partie 4 <../part4/index>` — **Projet & TPs**.

Une partie :doc:`QCM <../part6/index>` interactive complète le cours.


Ressources
=======================================

- *Data Mining: Concepts and Techniques* — Jiawei Han, Micheline Kamber, Jian Pei (3rd ed., Morgan Kaufmann, 2011).
- *Frequent Pattern Mining* — Charu C. Aggarwal, Jiawei Han.
- *Data Mining: The Textbook* — Charu C. Aggarwal.
- Implémentations de référence : `SPMF <https://www.philippe-fournier-viger.com/spmf/>`_, `FIMI <http://fimi.uantwerpen.be/>`_, `UCI ML Repository <https://archive.ics.uci.edu/>`_.


Contact et communication
=======================================

Les communications se feront par mail.

:Email: `John Aoga <johnaoga@gmail.com>`_


Cours Open-Source
=======================================

Les sources de ce site web sont open-source et disponibles sur `GitHub <https://github.com/johnaoga/gl-dm>`_.
N'hésitez pas à faire des pull requests si vous voyez des erreurs ou des éléments à corriger.

La licence utilisée est Creative Commons Attribution-ShareAlike 4.0 International License :

.. image:: https://i.creativecommons.org/l/by-sa/4.0/88x31.png
    :alt: CC-BY-SA
