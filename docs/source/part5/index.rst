.. _part5:

*************************************************************************************************
Chapitre 5 | Clustering et segmentation de données
*************************************************************************************************

Objectifs
=========

À l'issue de ce module, chaque étudiant.e sera capable de :

* Comprendre les concepts fondamentaux du clustering (apprentissage non supervisé)
* Différencier les types de clustering (partitionnement, hiérarchique, densité)
* Implémenter l'algorithme k-means
* Implémenter l'algorithme DBSCAN
* Implémenter le clustering hiérarchique
* Évaluer la qualité des clusters avec différentes métriques
* Appliquer le clustering à des données réelles (comportement client, analyse de trafic)
* Visualiser et interpréter les résultats de segmentation


Note de théorique
=======================================

Introduction au clustering
""""""""""""""""""""""""""

**Définition :**

Le clustering (ou partitionnement de données) est une technique d'apprentissage non supervisé qui consiste à regrouper des objets similaires en groupes appelés clusters, de sorte que :
- Les objets dans un même cluster sont similaires entre eux (intra-classe homogène)
- Les objets de clusters différents sont dissimilaires (inter-classe hétérogène)

**Différence avec la classification :**

.. code-block:: text

   Classification (supervisée)     | Clustering (non supervisé)
   --------------------------------|--------------------------------
   Labels connus à l'avance        | Pas de labels prédéfinis
   Apprentissage à partir d'exemples| Découverte de structure cachée
   Objectif : prédire              | Objectif : explorer, segmenter
   Ex: spam vs non-spam            | Ex: segmentation de clients

**Types de clustering :**

1. **Partitionnement** : Divise les données en k partitions disjointes
   - k-means, k-medoids

2. **Hiérarchique** : Crée une hiérarchie de clusters
   - Agglomératif (bottom-up)
   - Divisif (top-down)

3. **Basé sur la densité** : Clusters = régions denses séparées par régions peu denses
   - DBSCAN, OPTICS

**Applications du clustering :**

- Segmentation de clients (marketing)
- Détection d'anomalies
- Compression d'images
- Analyse de réseaux sociaux
- Analyse de trafic réseau
- Bioinformatique (groupement de gènes)


Mesures de similarité et distance
""""""""""""""""""""""""""""""""""

**Distance Euclidienne** (la plus courante) :

.. code-block:: text

   d(x, y) = √(Σᵢ(xᵢ - yᵢ)²)

**Distance de Manhattan** :

.. code-block:: text

   d(x, y) = Σᵢ|xᵢ - yᵢ|

**Importance de la normalisation :**

Les attributs avec grandes valeurs dominent le calcul de distance.

.. code-block:: text

   Exemple sans normalisation :
   Âge (20-80) vs Revenu (20000-100000)
   → Le revenu domine la distance !
   
   Solution : Normaliser ou standardiser les données


Algorithme k-means
""""""""""""""""""

**Principe :**

K-means est l'algorithme de clustering le plus populaire. Il partitionne n objets en k clusters où chaque objet appartient au cluster avec le centroïde le plus proche.

**Algorithme :**

1. **Initialisation** : Choisir k centroïdes initiaux (aléatoirement ou avec k-means++)

2. **Affectation** : Assigner chaque point au centroïde le plus proche
   
   .. code-block:: text
   
      Pour chaque point xᵢ :
          cluster(xᵢ) = argmin_j ||xᵢ - μⱼ||²

3. **Mise à jour** : Recalculer les centroïdes comme moyenne des points assignés
   
   .. code-block:: text
   
      Pour chaque cluster Cⱼ :
          μⱼ = (1/|Cⱼ|) × Σ_{x∈Cⱼ} x

4. **Répéter** les étapes 2-3 jusqu'à convergence (centroïdes ne changent plus)

**Exemple visuel du processus :**

.. code-block:: text

   Itération 0 (initialisation) :
   Points: •  •  •  •  •  •  •  •  •
   Centroïdes: ★  ◆  ▲

   Itération 1 (affectation) :
   •★• •★• •◆• •◆• •▲• •▲•

   Itération 1 (mise à jour centroïdes) :
   ★ se déplace vers le centre de son groupe

   Itération 2, 3, ... jusqu'à convergence

**Fonction objectif :**

K-means minimise la somme des carrés intra-cluster (within-cluster sum of squares - WCSS) :

.. code-block:: text

   J = Σⱼ Σ_{x∈Cⱼ} ||x - μⱼ||²

**Avantages de k-means :**

- Simple et facile à implémenter
- Rapide : O(n × k × i × d) où n=points, k=clusters, i=itérations, d=dimensions
- Efficace sur grands datasets
- Scalable

**Inconvénients de k-means :**

- Nécessite de spécifier k à l'avance
- Sensible à l'initialisation (peut converger vers optimum local)
- Sensible aux outliers
- Suppose des clusters sphériques et de tailles similaires

**Méthode du coude (Elbow Method) pour choisir k :**

1. Exécuter k-means pour différentes valeurs de k (ex: 1 à 10)
2. Calculer WCSS pour chaque k
3. Tracer WCSS en fonction de k
4. Chercher le "coude" dans la courbe

.. code-block:: text

   WCSS
    |
    |  \
    |   \
    |    \___
    |        \____
    |             \______
    +--------------------> k
          ↑ coude optimal

**Initialisation k-means++ :**

Amélioration de k-means qui choisit les centroïdes initiaux intelligemment :
1. Choisir le premier centroïde aléatoirement
2. Pour chaque point, calculer D(x) = distance au centroïde le plus proche
3. Choisir le prochain centroïde avec probabilité proportionnelle à D(x)²
4. Répéter jusqu'à avoir k centroïdes


Algorithme DBSCAN (Density-Based Spatial Clustering)
"""""""""""""""""""""""""""""""""""""""""""""""""""""

**Principe :**

DBSCAN identifie des clusters comme des régions de haute densité séparées par régions de faible densité. Il peut trouver des clusters de formes arbitraires et détecte automatiquement les outliers.

**Paramètres :**

- **ε (epsilon)** : Rayon de voisinage
- **MinPts** : Nombre minimum de points dans le voisinage pour définir une région dense

**Définitions :**

1. **ε-voisinage** de p : Tous les points à distance ≤ ε de p
   
   .. code-block:: text
   
      Nε(p) = {q | distance(p, q) ≤ ε}

2. **Point core** : Point ayant au moins MinPts points dans son ε-voisinage

3. **Point border** : Point dans le voisinage d'un point core, mais pas core lui-même

4. **Point noise (outlier)** : Ni core, ni border

**Algorithme DBSCAN :**

.. code-block:: text

   1. Marquer tous les points comme non visités
   2. Pour chaque point p non visité :
      a. Marquer p comme visité
      b. Trouver tous les points dans Nε(p)
      c. Si |Nε(p)| < MinPts :
         - Marquer p comme outlier (temporairement)
      d. Sinon :
         - Créer nouveau cluster C
         - Ajouter p à C
         - Pour chaque point q dans Nε(p) :
            * Si q non visité :
              - Marquer q comme visité
              - Trouver Nε(q)
              - Si |Nε(q)| ≥ MinPts : ajouter Nε(q) à Nε(p)
            * Si q n'appartient à aucun cluster : ajouter q à C

**Exemple visuel :**

.. code-block:: text

   MinPts = 4, ε = rayon du cercle
   
   Cluster 1:  ● ● ●     Cluster 2:    ● ● ●
               ● ● ●                   ● ● ●
               ● ● ●                   ● ● ●
   
   Outliers: •   •   •
   
   Points core: ●  Points border: ◐  Noise: •

**Avantages de DBSCAN :**

- Ne nécessite pas de spécifier k à l'avance
- Trouve des clusters de formes arbitraires
- Détecte automatiquement les outliers
- Robuste aux outliers

**Inconvénients de DBSCAN :**

- Sensible aux paramètres ε et MinPts
- Difficile avec clusters de densités variables
- Complexité O(n²) dans le pire cas (O(n log n) avec index spatial)

**Choix des paramètres :**

Pour **MinPts** :
- Règle générale : MinPts ≥ d + 1 (d = nombre de dimensions)
- Souvent : MinPts = 4 ou 5 pour données 2D

Pour **ε** :
- Méthode du k-distance graph :
  1. Pour chaque point, calculer distance au k-ème voisin le plus proche
  2. Trier ces distances en ordre décroissant
  3. Tracer le graphe
  4. Chercher le "coude" → valeur de ε


Clustering hiérarchique
"""""""""""""""""""""""

**Principe :**

Le clustering hiérarchique crée une hiérarchie de clusters représentée par un dendrogramme.

**Approche agglomérative (bottom-up)** :

- Commencer avec n clusters (chaque point = cluster)
- Fusionner itérativement les clusters les plus proches
- Arrêter quand il reste k clusters ou un seul cluster

**Algorithme agglomératif :**

.. code-block:: text

   1. Initialiser : chaque point = un cluster
   2. Calculer la matrice de distances entre tous les clusters
   3. Répéter jusqu'à avoir k clusters souhaités :
      a. Trouver les deux clusters les plus proches
      b. Fusionner ces clusters
      c. Mettre à jour la matrice de distances

**Méthodes de liaison (linkage) :**

1. **Single linkage (minimum)** :
   
   .. code-block:: text
   
      d(C₁, C₂) = min{d(x, y) | x ∈ C₁, y ∈ C₂}
   
   - Peut créer des chaînes, sensible aux outliers

2. **Complete linkage (maximum)** :
   
   .. code-block:: text
   
      d(C₁, C₂) = max{d(x, y) | x ∈ C₁, y ∈ C₂}
   
   - Préfère des clusters compacts

3. **Average linkage (moyenne)** :
   
   .. code-block:: text
   
      d(C₁, C₂) = moyenne{d(x, y) | x ∈ C₁, y ∈ C₂}
   
   - Compromis équilibré

4. **Ward (variance minimale)** :
   - Minimise la variance intra-cluster lors de fusion
   - Tend à créer des clusters de tailles égales

**Dendrogramme :**

Représentation graphique de la hiérarchie :

.. code-block:: text

        |
      __|__
     |     |
   __|__  _|_
  |    | |   |
  A    B C   D
  
  Hauteur = distance de fusion

**Avantages du clustering hiérarchique :**

- Pas besoin de spécifier k à l'avance
- Dendrogramme fournit une visualisation intuitive
- Déterministe (pas d'initialisation aléatoire)
- Peut révéler la structure hiérarchique

**Inconvénients :**

- Complexité O(n²) en temps et mémoire
- Difficile pour grands datasets
- Décisions de fusion irréversibles


Évaluation de la qualité des clusters
""""""""""""""""""""""""""""""""""""""

**1. Silhouette Score :**

Mesure la qualité du clustering en comparant la cohésion intra-cluster et la séparation inter-cluster.

.. code-block:: text

   Pour un point i :
   a(i) = distance moyenne entre i et les autres points de son cluster
   b(i) = distance moyenne entre i et les points du cluster voisin le plus proche
   
   s(i) = (b(i) - a(i)) / max(a(i), b(i))
   
   Silhouette globale = moyenne de s(i) pour tous les points

**Interprétation :**
- s(i) proche de 1 : point bien clustérisé
- s(i) proche de 0 : point sur la frontière entre clusters
- s(i) proche de -1 : point mal clustérisé

**2. Davies-Bouldin Index :**

Mesure le ratio entre la similarité intra-cluster et la dissimilarité inter-cluster.

**Interprétation :** Plus faible = meilleur clustering

**3. Calinski-Harabasz Index (Variance Ratio Criterion) :**

Ratio entre la variance inter-cluster et intra-cluster.

**Interprétation :** Plus élevé = meilleur clustering

**4. Inertia (WCSS) pour k-means :**

.. code-block:: text

   Inertia = Σⱼ Σ_{x∈Cⱼ} ||x - μⱼ||²

**Interprétation :** Plus faible = meilleur, mais attention à l'overfitting


Comparaison des algorithmes
""""""""""""""""""""""""""""

.. code-block:: text

   Algorithme   | Avantages              | Inconvénients           | Quand utiliser
   -------------|------------------------|-------------------------|------------------
   K-means      | Rapide                 | Nécessite k             | Clusters sphériques
                | Simple                 | Sensible init           | Grandes données
                | Scalable               | Sensible outliers       | Tailles similaires
   -------------|------------------------|-------------------------|------------------
   DBSCAN       | Formes arbitraires     | Paramètres difficiles   | Formes complexes
                | Détecte outliers       | Densités variables      | Présence outliers
                | Pas besoin de k        | Lent haute dimension    | Données spatiales
   -------------|------------------------|-------------------------|------------------
   Hiérarchique | Dendrogramme           | O(n²) complexité        | Petits datasets
                | Pas besoin de k        | Mémoire intensive       | Visualisation
                | Déterministe           | Irréversible            | Structure hiérarchique


Applications pratiques
""""""""""""""""""""""

**1. Segmentation de clients (comportement client) :**

.. code-block:: text

   Features : Âge, Revenu, Fréquence d'achat, Montant moyen
   Algorithme : k-means (3-5 segments)
   Résultat : Profils clients (VIP, Réguliers, Occasionnels)
   Action : Stratégies marketing ciblées

**2. Analyse de trafic réseau :**

.. code-block:: text

   Features : Volume, protocole, durée, destination
   Algorithme : DBSCAN (détection anomalies)
   Résultat : Trafic normal vs anomalies
   Action : Alertes de sécurité, détection d'intrusions

**3. Détection d'anomalies :**

.. code-block:: text

   Algorithme : DBSCAN
   Résultat : Points outliers = anomalies potentielles
   Action : Investigation, prévention fraude




Visualisation et interprétation
""""""""""""""""""""""""""""""""

**Techniques de visualisation :**

1. **Scatter plots 2D/3D** : Visualisation directe si 2-3 dimensions
2. **PCA** : Réduction à 2D pour visualisation si plus de dimensions
3. **Dendrogramme** : Pour clustering hiérarchique
4. **Silhouette plots** : Évaluation visuelle de la qualité

**Interprétation des résultats :**

1. **Profiler chaque cluster** :
   - Calculer les moyennes/médianes des features
   - Identifier les caractéristiques distinctives

2. **Nommer les clusters** :
   - Donner des noms significatifs selon le contexte
   - Ex: "Clients premium", "Utilisateurs occasionnels"

3. **Analyser les différences** :
   - Comparer les profils entre clusters
   - Identifier les variables discriminantes

4. **Valider avec le métier** :
   - Vérifier la pertinence avec des experts
   - Ajuster si nécessaire


À lire / Aller plus loin
=======================================

**Slides du cours :** [À compléter]

**Articles fondateurs :**


**Livres de référence :**


**Ressources en ligne et vidéos :**

- **StatQuest: K-means clustering (RECOMMANDÉ)** : https://www.youtube.com/watch?v=vgu7_evXua4&t=36s
  * Excellente explication visuelle et intuitive de k-means
  * Couvre l'algorithme, l'initialisation et la méthode du coude
- Scikit-learn Clustering Guide : https://scikit-learn.org/stable/modules/clustering.html
- DBSCAN Visualization : https://www.naftaliharris.com/blog/visualizing-dbscan-clustering/

**Datasets pour clustering :**

- Iris Dataset : Classification adaptable au clustering
- Mall Customer Segmentation : https://www.kaggle.com/datasets/vjchoudhary7/customer-segmentation-tutorial-in-python
- Wholesale customers : UCI Machine Learning Repository

**Aller plus loin :**



Exercices théoriques
=======================================

.. note::
   Vous devez faire ces exercices avant la prochaine séance.

Exercice 1 - K-means et DBSCAN sur petit dataset
"""""""""""""""""""""""""""""""""""""""""""""""""

Soit les points suivants en 2D :

.. code-block:: text

   Points : A(1,1), B(1,2), C(2,1), D(2,2), E(8,8), F(8,9), G(9,8), H(9,9)

**Partie A - K-means (k=2) :**

1. Initialisation : Choisir A(1,1) et E(8,8) comme centroïdes initiaux
2. **Itération 1** :
   - Affecter chaque point au centroïde le plus proche (calculer les distances euclidiennes)
   - Calculer les nouveaux centroïdes
   - Calculer l'inertia (WCSS)
3. **Itération 2** :
   - Réaffecter les points
   - Recalculer les centroïdes
   - Vérifier la convergence
4. Visualiser les résultats finaux
5. Que se passerait-il avec une initialisation différente ?

**Partie B - DBSCAN (ε=2, MinPts=3) :**

1. Pour chaque point, identifier son ε-voisinage (calculer distances)
2. Classifier chaque point comme core, border ou noise
3. Identifier les clusters formés
4. Comparer avec les résultats de k-means
5. Que se passe-t-il si ε=1.5 ? Si MinPts=4 ?

**Partie C - Analyse comparative :**

1. Quels sont les avantages de chaque méthode sur ces données ?
2. Ajoutez un point outlier O(15,15). Comment chaque algorithme réagit-il ?
3. Dans quel contexte réel (comportement client ou analyse trafic) préféreriez-vous chaque algorithme ?


Exercice 2 - Application à la segmentation client
""""""""""""""""""""""""""""""""""""""""""""""""""

Un magasin possède les données suivantes sur 6 clients :

.. code-block:: text

   Client | Fréquence_visites/mois | Montant_moyen_€ | Ancienneté_mois
   -------|------------------------|-----------------|----------------
   C1     | 20                     | 50              | 36
   C2     | 18                     | 55              | 40
   C3     | 2                      | 150             | 6
   C4     | 3                      | 140             | 8
   C5     | 10                     | 80              | 24
   C6     | 1                      | 200             | 3

**Questions :**

1. **Prétraitement** :
   - Pourquoi est-il nécessaire de normaliser ces données ?
   - Appliquez la standardisation (z-score) sur les trois attributs
   - Calculez les valeurs standardisées pour chaque client

2. **Application de k-means (k=2)** :
   - Choisissez C1 et C6 comme centroïdes initiaux
   - Effectuez 2 itérations de k-means sur les données standardisées
   - Quels sont les clusters finaux ?

3. **Interprétation métier** :
   - Donnez un nom à chaque cluster (ex: "Clients fidèles", "Nouveaux clients premium")
   - Décrivez le profil type de chaque cluster
   - Proposez une stratégie marketing spécifique pour chaque segment

4. **Évaluation** :
   - Calculez manuellement le silhouette score pour un point de chaque cluster
   - Les clusters sont-ils bien séparés ?

5. **Extension** :
   - Si vous deviez essayer k=3, quels pourraient être les 3 segments ?
   - Quels attributs supplémentaires pourriez-vous collecter pour améliorer la segmentation ?


Exercice pratique (TP3)
=======================================

.. note::
   Ce TP constitue une partie importante de votre évaluation (TP3).

Partie 1 : Implémentation de k-means
"""""""""""""""""""""""""""""""""""""

**Objectif :** Implémenter l'algorithme k-means from scratch en Python.

**Spécifications techniques :**

.. code-block:: python

   import numpy as np
   import matplotlib.pyplot as plt
   
   def euclidean_distance(point1, point2):
       """Calcule la distance euclidienne entre deux points"""
       pass
   
   def initialize_centroids(data, k, method='random'):
       """
       Initialise k centroïdes
       method: 'random' ou 'kmeans++'
       """
       pass
   
   def assign_clusters(data, centroids):
       """Assigne chaque point au centroïde le plus proche"""
       pass
   
   def update_centroids(data, labels, k):
       """Recalcule les centroïdes comme moyenne des points assignés"""
       pass
   
   def calculate_wcss(data, labels, centroids):
       """Calcule l'inertia (Within-Cluster Sum of Squares)"""
       pass
   
   def kmeans(data, k, max_iterations=100, method='random'):
       """
       Algorithme k-means complet
       Retourne: centroids, labels, wcss_history
       """
       pass
   
   def elbow_method(data, k_range):
       """
       Applique k-means pour différentes valeurs de k
       Retourne les WCSS pour la méthode du coude
       """
       pass

**Livrables :**
- Code Python avec implémentation complète
- Tests de validation
- Comparaison avec sklearn.cluster.KMeans


Partie 2 : Implémentation de DBSCAN
""""""""""""""""""""""""""""""""""""

**Objectif :** Implémenter l'algorithme DBSCAN from scratch en Python.

**Spécifications techniques :**

.. code-block:: python

   def get_neighbors(data, point_idx, epsilon):
       """Trouve tous les points dans le ε-voisinage"""
       pass
   
   def dbscan(data, epsilon, min_pts):
       """
       Algorithme DBSCAN complet
       Retourne: labels (où -1 = noise)
       """
       pass

**Livrables :**
- Code Python avec implémentation complète
- Tests sur données avec outliers
- Comparaison avec sklearn.cluster.DBSCAN


Partie 3 : Implémentation du clustering hiérarchique
"""""""""""""""""""""""""""""""""""""""""""""""""""""

**Objectif :** Implémenter le clustering hiérarchique agglomératif.

**Spécifications techniques :**

.. code-block:: python

   def compute_distance_matrix(data):
       """Calcule la matrice de distances entre tous les points"""
       pass
   
   def hierarchical_clustering(data, n_clusters, linkage='average'):
       """
       Clustering hiérarchique agglomératif
       linkage: 'single', 'complete', 'average'
       Retourne: labels, dendrogram_data
       """
       pass

**Livrables :**
- Code Python avec implémentation
- Visualisation du dendrogramme
- Comparaison avec scipy.cluster.hierarchy

