.. _part2:

*************************************************************************************************
Chapitre 2 | Préparation des données et analyse exploratoire
*************************************************************************************************

Objectifs
=========

À l'issue de ce module, chaque étudiant.e sera capable de :

* Comprendre l'importance et les étapes du prétraitement des données
* Appliquer les techniques de nettoyage de données (valeurs manquantes, outliers, normalisation)
* Effectuer une analyse exploratoire des données (EDA) complète
* Réduire la dimensionnalité des données lorsque nécessaire
* Visualiser efficacement les données pour en extraire des insights préliminaires
* Documenter le processus de préparation des données


Note de théorique
=======================================

L'importance du prétraitement des données
"""""""""""""""""""""""""""""""""""""""""""

Le prétraitement des données est une étape cruciale en data mining, souvent considérée comme la plus longue et la plus importante. Selon diverses études, elle peut représenter 60-80% du temps total d'un projet de data mining.

**Problématiques courantes des données brutes :**
- Données incomplètes (valeurs manquantes)
- Données bruitées (erreurs, incohérences)
- Données redondantes (duplications)
- Données non standardisées (unités différentes, formats variés)
- Données déséquilibrées (classes sous-représentées)

**Phases du prétraitement :**
1. **Nettoyage** : Traitement des valeurs manquantes, outliers, incohérences
2. **Intégration** : Combinaison de sources de données multiples
3. **Transformation** : Normalisation, discrétisation, agrégation
4. **Réduction** : Réduction de dimensionnalité, échantillonnage


Techniques de nettoyage de données
""""""""""""""""""""""""""""""""""

**Valeurs manquantes :**
- **Suppression** : Éliminer les instances ou attributs avec trop de valeurs manquantes
  *Limite* : Peut entraîner une perte importante d'information
- **Imputation** : Remplacer par :
  * Moyenne/médiane pour les attributs numériques
  * Mode pour les attributs catégoriels
  * Valeurs prédites par des modèles (k-NN, régression)
  * Constante spécifique (ex: "unknown")




Analyse exploratoire des données (EDA)
""""""""""""""""""""""""""""""""""""""

L'EDA est une approche pour analyser les jeux de données afin de résumer leurs principales caractéristiques, souvent avec des méthodes visuelles.

**Étapes de l'EDA :**



**Visualisations clés :**
- **Histogrammes** : Distribution d'une variable
- **Boxplots** : Distribution et outliers
- **Scatter plots** : Relation entre deux variables numériques
- **Heatmaps** : Matrices de corrélation
- **Pairplots** : Relations multiples entre variables


Réduction de dimensionnalité
""""""""""""""""""""""""""""

**Pourquoi réduire la dimensionnalité ?**
- Réduction du temps de calcul
- Éviter le "curse of dimensionality"
- Amélioration de la visualisation
- Réduction du bruit




Documentation du processus
""""""""""""""""""""""""""

**Éléments à documenter :**
1. Description initiale du dataset
2. Problèmes identifiés (valeurs manquantes, outliers, etc.)
3. Techniques appliquées pour chaque problème
4. Justification des choix techniques
5. Impact des transformations sur les données
6. Visualisations avant/après prétraitement


À lire / Aller plus loin
=======================================

Slides du cours : 

Livres de référence :

Aller plus loin :


Exercices théoriques
=======================================

.. note::
   Vous devez faire ces exercices avant la prochaine séance.

Exercice 1 - Analyse critique
"""""""""""""""""""""""""""""

Vous recevez un dataset contenant les informations suivantes sur des clients :
- Âge (numérique, avec valeurs négatives et >120)
- Revenu annuel (numérique, avec 30% de valeurs manquantes)
- Niveau d'éducation (catégoriel : "Bac", "Licence", "Master", "PhD", "Autre")
- Dépenses mensuelles (numérique, avec des outliers évidents)
- Type de compte (catégoriel : "Standard", "Premium", None)

1. Identifiez au moins 5 problèmes de qualité des données dans ce dataset
2. Proposez une stratégie de traitement pour chaque problème identifié
3. Justifiez vos choix techniques

Exercice 2 - Techniques de prétraitement
""""""""""""""""""""""""""""""""""""""""

Pour chaque scénario ci-dessous, indiquez la technique de prétraitement la plus appropriée et expliquez pourquoi :

1. Un attribut "température" avec des valeurs allant de -10 à 40°C doit être utilisé dans un algorithme de clustering sensible à l'échelle
2. Un dataset contient 50 attributs fortement corrélés entre eux
3. La variable cible dans un problème de classification est déséquilibrée (95% classe A, 5% classe B)
4. Un attribut "code postal" est représenté sous différents formats (ex: "1000", "1,000", "1 000")
5. Vous avez un dataset avec 1 million d'instances et 1000 attributs, mais seulement 10 Go de RAM disponible

Exercice 3 - Visualisation et interprétation
""""""""""""""""""""""""""""""""""""""""""""

Imaginez que vous avez réalisé les visualisations suivantes lors d'une EDA :

1. Un histogramme d'âge montrant une distribution bimodale avec pics autour de 25-30 ans et 50-55 ans
2. Un scatter plot revenu vs dépenses montrant une relation linéaire positive avec quelques outliers extrêmes
3. Une heatmap de corrélation montrant que 10 attributs sont fortement corrélés (r > 0.9)
4. Un boxplot montrant que la variable "temps de chargement" a de nombreux outliers vers les hautes valeurs

Pour chaque visualisation :
- Que pouvez-vous conclure sur les données ?
- Quelles implications pour la suite du projet de data mining ?
- Quelles actions recommanderiez-vous ?

Exercice pratique
""""""""""""""""""

Utilisez le dataset "Online Retail" (disponible sur UCI) et réalisez :

1. **Chargement et inspection initiale** :
   - Nombre d'instances et attributs
   - Types de données
   - Présence de valeurs manquantes

2. **Nettoyage des données** :
   - Traitez les valeurs manquantes de manière appropriée
   - Identifiez et traitez les outliers dans "Quantity" et "UnitPrice"
   - Standardisez les données numériques si nécessaire

3. **Analyse exploratoire** :
   - Calculez les statistiques descriptives pour chaque attribut numérique
   - Analysez la distribution des pays des clients
   - Étudiez la relation entre quantité et prix unitaire
   - Identifiez les produits les plus vendus

4. **Visualisation** :
   - Créez au moins 5 visualisations différentes et pertinentes
   - Interprétez chaque visualisation
   - Notez vos insights principaux

5. **Documentation** :
   - Créez un rapport succinct décrivant votre processus
   - Justifiez chaque décision de prétraitement
   - Incluez vos visualisations et leur interprétation