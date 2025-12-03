.. _part1:

*************************************************************************************************
Chapitre 1 | Introduction au Data Mining et processus CRISP-DM
*************************************************************************************************

Objectifs
=========

À l'issue de ce module, chaque étudiant.e sera capable de :

* Différencier les notions d'IA, Data Science, Machine Learning, Deep Learning et Data Mining
* Expliquer les étapes du processus CRISP-DM (Cross-Industry Standard Process for Data Mining)
* Identifier les cas d'usage typiques du data mining dans différents domaines
* Réaliser une analyse descriptive basique d'un jeu de données
* Comprendre le rôle du data mining dans le cycle de découverte des connaissances (KDD)


Note de théorique
=======================================

Exercice introductif
""""""""""""""""""""

1. Donnez trois exemples concrets où vous avez observé l'utilisation de techniques d'analyse de données dans votre vie quotidienne (ex: recommandations Netflix, détection de fraude bancaire, etc.).
2. À votre avis, quelles sont les principales difficultés lorsqu'on essaie de trouver des "patterns" ou tendances dans un grand volume de données ?
3. Si vous aviez un dataset de transactions clients d'un supermarché, quels types de questions pourriez-vous poser pour mieux comprendre le comportement d'achat ?
4. Comment l'extraction de connaissances à partir de données peut-elle créer de la valeur pour une entreprise ou une organisation ?

Notes
""""""
- La notion de Data Mining (fouille de données) : processus d'extraction de connaissances à partir de grands volumes de données
- Différenciation des concepts :
  * Intelligence Artificielle (IA) : systèmes capables de simuler l'intelligence humaine
  * Data Science : discipline englobant la collecte, le traitement, l'analyse et la visualisation des données
  * Machine Learning (ML) : sous-domaine de l'IA permettant aux systèmes d'apprendre à partir des données
  * Deep Learning (DL) : sous-domaine du ML utilisant des réseaux de neurones profonds
  * Data Mining : techniques spécifiques pour découvrir des patterns dans les données
- Le processus KDD (Knowledge Discovery in Databases) :
  1. Sélection des données
  2. Prétraitement
  3. Transformation
  4. Data Mining
  5. Interprétation/Évaluation
- Le processus CRISP-DM (6 étapes) :
  1. Compréhension métier (Business Understanding)
  2. Compréhension des données (Data Understanding)
  3. Préparation des données (Data Preparation)
  4. Modélisation (Modeling)
  5. Évaluation (Evaluation)
  6. Déploiement (Deployment)
- Domaines d'application du data mining :
  * Marketing (analyse du panier client)
  * Finance (détection de fraude)
  * Santé (diagnostic assisté)
  * Télécommunications (analyse de comportement)
  * Transport (optimisation des trajets)
- Types de patterns recherchés :
  * Associations (règles d'association)
  * Séquences (patterns temporels)
  * Classes (classification)
  * Clusters (groupes similaires)
  * Anomalies (détection d'exceptions)
- Outils et technologies courants :
  * Python (pandas, scikit-learn, mlxtend)
  * R
  * SQL avancé


À lire / Aller plus loin
=======================================

Slides du cours : 

Livres de référence :

Aller plus loin :



Exercices théoriques
=======================================

.. note::
   Vous devez faire ces exercices avant la prochaine séance.

Exercice 1
""""""""""""""

- Quelles sont les différences principales entre Data Mining et Machine Learning ?
- Pourquoi le processus CRISP-DM est-il considéré comme un standard industriel ?
- Citez trois domaines où le data mining a transformé les pratiques et expliquez comment.


Exercice pratique préparatoire
""""""""""""""""""""""""""""""

1. Téléchargez le dataset Iris depuis UCI ou utilisez directement depuis sklearn
2. Effectuez une analyse descriptive basique :
   - Nombre d'instances et d'attributs
   - Types de données
   - Statistiques descriptives (moyenne, médiane, écart-type pour les attributs numériques)
   - Distribution des classes pour l'attribut cible
3. Visualisez les données avec au moins deux types de graphiques (histogramme, scatter plot)
4. Notez vos premières observations et hypothèses
