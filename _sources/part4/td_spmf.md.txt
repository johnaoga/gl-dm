(td_spmf)=

# Travaux Dirigés : se familiariser avec un outil de Data Mining

> **Attention!**: Relire la politique sur l'IA si nécessaire si vous n'êtes pas sure de ce qu'il faut faire dans le cas d'un TD! Sinon, c'est 0 autorisation. C'est vraiment pour vous exercer et il n'y a pas de colle dans ce qui vous est demandé et il n'y a pas de bonnes/mauvaises réponses. En vrai il s'agit de réapprendre à lire, à chercher par soi-même à l'ère des IA et c'est un bon exercice. Faites le consciemment et honnêtement!

**Cours :** Data Mining / Fouille de données
**Thème :** Extraction d'itemsets fréquents (*Frequent Itemset Mining*, FIM) avec la bibliothèque SPMF
**Modalité :** individuel
**Durée indicative :** 4 heures
**Date limite de remise :** 02/10/2026 à 18H00 (heure locale)
**Lien de remise (Google Form) :** https://forms.gle/AULbnicQwN9XKKgV7

---

## 1. Contexte

L'extraction d'itemsets fréquents consiste à découvrir, dans une base de transactions, les ensembles d'éléments (*items*) qui apparaissent ensemble suffisamment souvent. C'est le problème à l'origine de l'analyse du « panier de la ménagère » et des règles d'association. L'algorithme **Apriori**, proposé par Agrawal et Srikant en 1994, est l'algorithme fondateur de ce domaine.

Dans ce TD, vous allez découvrir **SPMF**, une bibliothèque open source spécialisée dans la fouille de motifs, l'utiliser via son interface graphique puis depuis un langage de programmation, et mettre vos résultats en perspective avec l'article original d'Apriori.

## 2. Objectifs pédagogiques

À l'issue de ce TD, vous devez être capable de :

1. **Présenter** la bibliothèque SPMF : ce qu'elle est, ce qu'elle propose, comment elle est distribuée et documentée.
2. **Utiliser** l'interface graphique de SPMF pour charger un jeu de données, paramétrer et exécuter un algorithme, et lire le fichier de résultats.
3. **Comprendre** le format des données transactionnelles et **interpréter** un itemset fréquent et son support.
4. **Situer** l'algorithme Apriori dans son contexte scientifique en identifiant et en parcourant l'article original.
5. **Automatiser** l'exécution d'un algorithme SPMF depuis Python ou R et **vérifier** que les résultats sont identiques à ceux de l'interface graphique.
6. **Mener une petite expérimentation** (temps d'exécution en fonction du support minimum), la représenter graphiquement et l'analyser.
7. **Porter un regard critique** sur les limites identifiées en 1994 à la lumière des moyens de calcul actuels.

## 3. Prérequis techniques

- **Java** (version 8 ou plus récente) installé. Vérifiez avec `java -version`.
- **Python 3** (avec `matplotlib`, éventuellement `pandas`) **ou** **R** (avec ggplot2 ou autres).
- Un éditeur de texte / IDE et un terminal.

---

## 4. Travail à réaliser

> Conseil : prenez des notes et des captures d'écran au fur et à mesure. Plusieurs questions du formulaire de remise vous demanderont des informations précises (versions, nombres, valeurs) que vous ne pourrez obtenir qu'en ayant réellement effectué les manipulations. Evidemment vous pouvez copier pour un ami, mais je me demande à quoi ça t'avancera aujourd'hui!

### Partie A — Découvrir SPMF

1. Rendez-vous sur le site officiel : <https://www.philippe-fournier-viger.com/spmf/>
2. Lisez la page d'accueil et répondez pour vous-même aux questions suivantes : Qu'est-ce que SPMF ? Dans quel langage est-elle écrite ? Sous quelle licence est-elle distribuée ? Quels grands types de motifs permet-elle d'extraire (itemsets, règles d'association, motifs séquentiels, etc.) ? Combien d'algorithmes environ propose-t-elle ?
3. Retrouvez le **dépôt GitHub** officiel de SPMF (à partir du site ou par une recherche). Explorez-le : organisation des dossiers, où se trouve le code de l'algorithme Apriori, date de la dernière mise à jour, présence d'une licence, etc.
4. Téléchargez le fichier **`spmf.jar`** (dernière version) depuis la page de téléchargement du site. Notez le numéro de version.

### Partie B — L'article fondateur d'Apriori

1. Retrouvez l'article suivant (version PDF) :
   *R. Agrawal et R. Srikant, « Fast Algorithms for Mining Association Rules », Proceedings of the 20th International Conference on Very Large Data Bases (VLDB), 1994.*
2. Il n'est **pas** demandé de lire l'article en entier. Vous devez :
   - lire attentivement le **résumé** (*abstract*) -- Hey! Lire un paragraphe en anglais et le comprendre est un skill intéressant à avoir donc n'essayer pas de traduire directement, relisez une ou deux fois d'abord;
   - parcourir l'article pour en comprendre la **structure** (sections, nature des expérimentations, figures) ;
   - repérer la **Figure 6** (vous en aurez besoin dans la Partie G) ;
   - lire la **conclusion** et les **perspectives** (vous en aurez besoin dans la Partie H).

### Partie C — L'exemple Apriori de la documentation SPMF

1. Lisez la page d'exemple : <https://www.philippe-fournier-viger.com/spmf/Apriori.php>
2. Assurez-vous de comprendre :
   - le **format du fichier d'entrée** (une ligne = une transaction ; les items sont des entiers positifs séparés par des espaces) ;
   - la signification du paramètre **minsup** (support minimum, exprimé en pourcentage) ;
   - le **format du fichier de sortie** (chaque ligne contient un itemset suivi de `#SUP:` et de son support absolu, c'est-à-dire le nombre de transactions qui le contiennent) ;
   - la différence entre **support absolu** (nombre de transactions) et **support relatif** (pourcentage).
3. Reproduisez « à la main » ou mentalement le petit exemple de la page pour vérifier votre compréhension.

### Partie D — Les jeux de données *retail* et *chess*

1. Rendez-vous sur la page des jeux de données : <https://www.philippe-fournier-viger.com/spmf/index.php?link=datasets.php>
2. Téléchargez les jeux de données **retail** et **chess** (format SPMF pour l'extraction d'itemsets).
3. Documentez-vous sur chacun :
   - D'où proviennent-ils ? Que représente une transaction ? Que représente un item ?
   - Combien de transactions, combien d'items distincts, quelle longueur moyenne de transaction ?
   - Lequel est **dense** et lequel est **creux** (*sparse*) ? Pourquoi est-ce important pour un algorithme comme Apriori ?
4. Ouvrez les fichiers dans un éditeur de texte et observez leur contenu.

> **Attention :** ces fichiers ne sont pas des tableaux à colonnes fixes. Réfléchissez à ce que représente chaque nombre sur une ligne, et pourquoi deux lignes n'ont pas forcément la même longueur.

### Partie E — Prise en main de l'interface graphique

1. Lancez l'interface graphique de SPMF : double-cliquez sur `spmf.jar`, ou dans un terminal :
   ```bash
   java -jar spmf.jar
   ```
   Si vous manquez de mémoire sur de gros jeux de données, vous pouvez augmenter la mémoire allouée à Java, par exemple : `java -Xmx4g -jar spmf.jar`.
2. Dans l'interface :
   - choisissez l'algorithme **Apriori** ;
   - sélectionnez le fichier d'entrée **retail** ;
   - choisissez un fichier de sortie ;
   - fixez **minsup** (par exemple `10%`, puis d'autres valeurs) ;
   - exécutez et observez les statistiques affichées (temps d'exécution, mémoire, nombre d'itemsets fréquents).
3. Ouvrez le fichier de sortie et examinez les itemsets trouvés.
4. **Interprétation :** choisissez **un itemset de taille au moins 2** issu de *retail* et expliquez en une ou deux phrases ce qu'il signifie concrètement (quels items, combien de transactions, quel pourcentage des transactions). Calculez vous-même son support relatif à partir du support absolu.
5. Répétez l'exécution sur **chess** avec un support minimum élevé (par exemple `90%`) et comparez qualitativement le nombre d'itemsets obtenus avec *retail*.
6. Faites une **capture d'écran** de l'interface après une exécution (paramètres et statistiques visibles).

### Partie F — Utiliser SPMF depuis un programme

1. Sur le site de SPMF, repérez la section consacrée aux **wrappers** (interfaces permettant d'appeler SPMF depuis d'autres langages).
2. Choisissez **Python ou R**.
   - **En Python**, l'approche recommandée est d'appeler directement le `.jar` en ligne de commande depuis votre script (par exemple avec le module `subprocess`). Soyez libre d'utiliser aussi `SPMF.py`. La commande SPMF en ligne de commande a la forme générale :
     ```bash
     java -jar spmf.jar run Apriori retail.txt output.txt 1%
     ```
   - **En R**, vous pouvez utiliser un wrapper existant ou appeler le `.jar` via `system()` / `system2()`.
3. Écrivez un script qui :
   - exécute Apriori sur un jeu de données avec un support donné ;
   - lit le fichier de sortie et en extrait les itemsets et leurs supports ;
   - affiche le nombre d'itemsets fréquents trouvés.
4. **Vérification :** exécutez votre script avec **exactement les mêmes paramètres** que dans la Partie E et vérifiez que vous obtenez **les mêmes résultats** (même nombre d'itemsets, mêmes supports). Expliquez comment vous l'avez vérifié.

### Partie G — Expérimentation : temps d'exécution en fonction du support

1. Avec votre script, exécutez Apriori pour **plusieurs valeurs de support minimum** (au moins 5 valeurs) et mesurez à chaque fois :
   - le **temps d'exécution** ;
   - le **nombre d'itemsets fréquents** obtenus.

   Valeurs indicatives (à adapter selon votre machine) :
   - *chess* (dense) : 95 %, 90 %, 85 %, 80 %, 75 %, 70 % ;
   - *retail* (creux) : 50 %, 40 %, 30 %, 20 %, 10 %, 5 %, 2 %, 1 %.

   > Sur un jeu dense comme *chess*, le nombre d'itemsets explose très vite lorsque le support diminue. Si une exécution dépasse quelques minutes ou sature la mémoire, arrêtez-la et **mentionnez-le** dans votre rapport : c'est un résultat en soi.
2. Précisez **comment** vous mesurez le temps (temps mesuré par votre script autour de l'appel, ou temps affiché par SPMF). Idéalement, répétez chaque mesure plusieurs fois (par exemple 3) et retenez la moyenne.
3. Tracez une **courbe** (*line plot*) : support minimum en abscisse, temps d'exécution en ordonnée. Vous pouvez ajouter une seconde courbe ou un second graphique pour le nombre d'itemsets.
4. **Analysez** la tendance : comment évolue le temps lorsque le support diminue ? Le temps suit-il le nombre d'itemsets ? Les comportements de *retail* et *chess* diffèrent-ils, et pourquoi ?
5. **Comparez** la tendance de votre courbe avec la **Figure 6** de l'article de 1994 : que représente cette figure (axes, jeux de données, algorithmes comparés) ? La tendance est-elle la même que la vôtre ? Quelles différences de contexte (données, machine, ordres de grandeur) faut-il garder en tête pour comparer ?

### Partie H — Regard critique : de 1994 à aujourd'hui

À partir de la conclusion et des perspectives de l'article :

1. Identifiez les **difficultés et limites** auxquelles les auteurs étaient confrontés en 1994 (taille des bases, mémoire, accès disque, temps de calcul, types de données, etc.) et les **pistes de travail futur** qu'ils proposaient.
2. Discutez-les avec un **regard actuel** : ces problèmes sont-ils résolus ? Qu'ont changé la puissance de calcul, la mémoire, le calcul parallèle et distribué (clusters, GPU, supercalculateurs), le *cloud*, ou l'essor de l'IA ? Quels problèmes restent pertinents aujourd'hui (par exemple l'explosion combinatoire du nombre de motifs, illustrée par votre expérience sur *chess*) ?

---

## 5. Livrables

### 5.1 Le rapport

- **Une page de contenu** (format A4, police de 11 pt minimum, marges raisonnables).
- **Jusqu'à 2 pages supplémentaires** réservées **uniquement** aux références, tableaux, figures et captures d'écran.
- Format **PDF**, nommé `NOM_Prenom_TD_SPMF.pdf`.

**Ce qui est attendu dans la page de contenu** (à titre indicatif) :

- une présentation brève de SPMF et du problème FIM (2 à 3 phrases) ;
- l'interprétation d'un itemset de *retail* ;
- la manière dont vous avez vérifié la cohérence entre interface graphique et script ;
- l'analyse de votre courbe temps/support et sa comparaison avec la Figure 6 ;
- votre discussion critique 1994 → aujourd'hui.

Soyez **synthétique et précis** : une page, c'est court. Privilégiez les observations chiffrées et les arguments plutôt que la paraphrase de la documentation. Les figures doivent être lisibles, avoir des axes titrés avec unités, et être référencées dans le texte (« voir Figure 1 »).

**Dans les pages annexes :**

- la (ou les) figure(s) temps/support ;
- un tableau des mesures (support, temps, nombre d'itemsets) ;
- une capture d'écran de l'interface graphique ;
- les références (au minimum : le site SPMF et l'article d'Agrawal et Srikant).

### 5.2 Le formulaire de remise

La remise se fait **exclusivement** via le Google Form : https://forms.gle/AULbnicQwN9XKKgV7

Le formulaire contient des **questions précises** portant sur chaque partie du TD (informations factuelles, valeurs obtenues, interprétations) ainsi qu'un champ pour **téléverser votre rapport PDF**. Répondez directement et précisément ; les réponses doivent être cohérentes avec votre rapport. Prévoyez également de pouvoir fournir votre script (copié dans le formulaire ou via un lien).

### 5.3 Critères d'évaluation (indicatifs)

- Exactitude des réponses factuelles du formulaire (SPMF, données, article)
- Utilisation correcte de l'interface graphique et interprétation d'un itemset
- Script fonctionnel et vérification de cohérence GUI / script
- Qualité de l'expérimentation, de la figure et de l'analyse de tendance
- Comparaison avec la Figure 6 et discussion critique
- Clarté, concision et respect du format du rapport

---

## 6. Références

- P. Fournier-Viger et al., *SPMF: An Open-Source Data Mining Library*. <https://www.philippe-fournier-viger.com/spmf/>
- R. Agrawal et R. Srikant, « Fast Algorithms for Mining Association Rules », *Proceedings of the 20th International Conference on Very Large Data Bases (VLDB)*, Santiago, Chili, 1994.
- Documentation SPMF — exemple Apriori : <https://www.philippe-fournier-viger.com/spmf/Apriori.php>
- Jeux de données SPMF : <https://www.philippe-fournier-viger.com/spmf/index.php?link=datasets.php>
