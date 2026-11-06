(td_prefixspan)=

# Travaux Dirigés : explorer les séquences avec le principe « diviser pour régner »

**Cours :** Data Mining
**Thème :** Fouille de motifs séquentiels (*Sequential Pattern Mining*) — du parcours en profondeur naïf à l'algorithme PrefixSpan
**Modalité :** individuel
**Durée indicative :** 04 heures
**Date limite de remise :** 06/11/2026 18h00
**Lien de remise (Google Form) :** <https://forms.gle/KZup99XqLkCEXbf49>

---

## 1. Contexte

Dans une base de transactions, l'ordre des items n'a pas d'importance : {pain, lait} = {lait, pain}. Mais beaucoup de données sont **ordonnées** : les pages visitées par un internaute, les mots d'une phrase, les étapes d'un parcours de soins, les appels système d'un programme. On ne cherche alors plus des *ensembles* fréquents mais des **séquences** fréquentes, et l'espace de recherche change de nature : il devient **infini**.

Comment explorer un espace infini ? Ce TD vous fait construire la réponse pas à pas. Vous partirez d'un parcours en profondeur (DFS) naïf qui ne s'arrête jamais, vous mesurerez l'explosion du nombre de séquences possibles, puis vous introduirez les notions de sous-séquence, de plongement, de support, de préfixe, de suffixe et de **base projetée**. Assemblées, ces briques forment **PrefixSpan** (Pei et al., 2001), un algorithme qui applique le principe **« diviser pour régner »** : l'ensemble des motifs est découpé selon leur préfixe, et chaque sous-problème est résolu sur une base de données plus petite. Vous comparerez enfin votre implémentation à celle de la bibliothèque **SPMF**.

## 2. Objectifs pédagogiques

À l'issue de ce TD, vous devez être capable de :

1. **Expliquer** pourquoi un DFS naïf sur l'espace des séquences ne termine pas, et **borner** son exploration par une profondeur maximale.
2. **Distinguer** une séquence d'un ensemble (répétitions possibles, ordre significatif).
3. **Calculer** théoriquement le nombre de séquences de longueur au plus *d* sur un alphabet de *m* symboles, et **vérifier** ce calcul expérimentalement (comptage, temps, courbe).
4. **Définir** et **implémenter** les notions de sous-séquence, de plongement (*embedding*), de support, de préfixe et de suffixe.
5. **Construire** la base projetée d'une base de séquences par rapport à un préfixe, d'abord à la main puis par programme.
6. **Assembler** ces fonctions en une implémentation complète de PrefixSpan et en **valider** les résultats (force brute, SPMF).
7. **Mener** une comparaison expérimentale avec SPMF et en **rendre compte** de façon concise et chiffrée (rapport en anglais).

## 3. Prérequis techniques

- **Python 3.9+** avec `matplotlib` (et éventuellement `pandas`). Vérifiez avec `python3 --version`.
- **Java 8+** pour SPMF. Vérifiez avec `java -version`.
- Le fichier **`spmf.jar`** (dernière version), à télécharger depuis <https://www.philippe-fournier-viger.com/spmf/> (page *Download*).
- Un éditeur / IDE, un terminal. Un notebook Jupyter est autorisé.

**Convention utilisée dans tout le TD.** Dans le cas général, une séquence est une liste ordonnée d'*itemsets* (par exemple ⟨(a b) c⟩). Pour se concentrer sur l'essentiel, **chaque élément de nos séquences est un seul item**. Une séquence s'écrit donc comme une chaîne ou une liste : `abcac` ou `['a','b','c','a','c']`. Les jeux de données réels choisis en Partie E respectent cette convention.

Base de séquences jouet utilisée dans les Parties C à E (alphabet Σ = {a, b, c, d}) :

| id | séquence |
|---|---|
| S1 | `a b c a c` |
| S2 | `a c b c` |
| S3 | `b a b c` |
| S4 | `a b d` |
| S5 | `c b a` |

```python
DB = [list("abcac"), list("acbc"), list("babc"), list("abd"), list("cba")]
```

---

## 4. Travail à réaliser

> Conseil : travaillez dans un seul fichier (ou notebook) que vous enrichirez de partie en partie, et notez au fur et à mesure les valeurs affichées par vos programmes. Le formulaire de remise vous demandera des valeurs **exactes** sur la base jouet et sur vos expériences.

### Partie A — Le DFS naïf : explorer « toutes les possibilités »

On veut énumérer toutes les séquences que l'on peut écrire avec l'alphabet Σ = {a, b, c}. Idée naïve : partir de la séquence vide et, à chaque appel, **brancher sur chaque symbole** possible.

1. Implémentez ce parcours en profondeur, sans aucune condition d'arrêt :
   ```python
   def dfs_naif(seq, alphabet):
       for x in alphabet:
           nouvelle = seq + [x]
           print("".join(nouvelle))
           dfs_naif(nouvelle, alphabet)

   dfs_naif([], ["a", "b", "c"])
   ```
2. Exécutez-le et **observez** : quelles séquences sont affichées, dans quel ordre ? Comment le programme se termine-t-il (ou pas) ? Notez le message d'erreur éventuel et la longueur de la dernière séquence affichée.
3. Répondez pour vous-même : la séquence `b` a-t-elle été affichée ? Pourquoi ? Dessinez les 3 premiers niveaux de l'arbre d'exploration et repérez la branche dans laquelle le DFS « tombe » sans jamais en sortir.

   > L'arrêt que vous observez en Python vient d'une limite technique (`sys.getrecursionlimit()`), pas de l'algorithme. Une version itérative avec une pile ne s'arrêterait jamais (jusqu'à épuisement de la mémoire). Faites le lien avec la notion de **branche infinie**.
4. Ajoutez un paramètre de **profondeur** : la profondeur est **incrémentée** à chaque appel récursif, et l'exploration s'arrête lorsqu'elle atteint `d_max`.
   ```python
   def dfs_borne(seq, alphabet, profondeur, d_max, resultats):
       ...
   ```
   Exécutez-le pour `d_max = 2` puis `d_max = 3` et listez les séquences obtenues.
5. **Qu'avons-nous énuméré ?** Observez la liste obtenue pour `d_max = 2` :
   - Contient-elle `aa` ? `ab` **et** `ba` ? Ces deux dernières sont-elles « la même chose » ?
   - Comparez avec les **itemsets** construits sur {a, b, c} : combien y a-t-il d'itemsets non vides possibles ? Ce nombre est-il fini ?
   - Concluez : les objets énumérés sont des **séquences** parce que (i) les **répétitions** sont possibles et (ii) l'**ordre** compte (permuter les éléments donne un autre objet). C'est exactement ce qui rend l'espace infini.

### Partie B — Combien de possibilités ? Théorie et pratique

Notez *m* = |Σ| la taille de l'alphabet et *d* la profondeur maximale.

1. **Théorie.**
   - Combien existe-t-il de séquences de longueur **exactement** *k* ? Justifiez.
   - Déduisez-en le nombre total N(m, d) de séquences non vides de longueur **au plus** *d* (somme géométrique, donnez une forme close).
   - Calculez N(3, 2), N(3, 3) et N(3, 10) à la main ou à la calculatrice.
   - Que vaut le rapport N(m, d+1) / N(m, d) quand *d* devient grand ? Qu'en déduisez-vous sur l'évolution du temps de calcul lorsque l'on augmente la profondeur de 1 ?
2. **Pratique.** Écrivez deux versions de votre DFS borné :
   - `compter(m, d)` qui **compte** les séquences sans les stocker ;
   - `generer(m, d)` qui les **stocke** dans une liste.
   Vérifiez que vos comptes coïncident avec la formule pour les trois valeurs ci-dessus.
3. **Expérience.** Faites varier *d* pour au moins trois tailles d'alphabet. Valeurs indicatives :
   - *m* = 2 : *d* = 1 … 20 ;
   - *m* = 3 : *d* = 1 … 14 ;
   - *m* = 4 : *d* = 1 … 11.

   Pour chaque couple (*m*, *d*), mesurez le **nombre de séquences** et le **temps** (avec `time.perf_counter()`, moyenne de 3 répétitions). Si une exécution dépasse ~60 s ou sature la mémoire (`generer`), arrêtez-vous là pour cet alphabet et **notez-le** : c'est un résultat.
4. **Courbes.** Tracez le temps en fonction de *d* (une courbe par *m*), puis le nombre de séquences en fonction de *d*. Utilisez une **échelle logarithmique** sur l'axe des ordonnées : quelle forme ont les courbes, et pourquoi ? Superposez la courbe théorique N(m, d).
5. **Analyse.** Le temps est-il proportionnel au nombre de séquences ? Quelle différence observez-vous entre `compter` et `generer` (temps, mémoire) ?
6. **Pour aller plus loin (facultatif).** On peut aussi augmenter la profondeur **progressivement** (*d* = 1, puis 2, puis 3… en relançant le DFS à chaque fois : *iterative deepening*). Combien de séquences au total sont visitées pour atteindre la profondeur *D* ? Comparez avec un seul DFS à profondeur *D* (rapport approximatif en fonction de *m*).

> Retenez l'ordre de grandeur : même pour 3 symboles et des séquences de longueur 14, on dépasse plusieurs millions de candidats. Sur un vrai jeu de données (des centaines ou des milliers d'items différents), l'énumération exhaustive est hors de question. Il faut un moyen de **ne pas explorer** les branches inutiles.

### Partie C — Sous-séquences, plongements, support, préfixes et suffixes

On travaille avec la séquence `s = a b c a c` (positions 0 à 4) puis avec la base jouet `DB` (Section 3).

1. **Sous-séquence (sur papier).** Une séquence t = ⟨t₁ … t_k⟩ est une **sous-séquence** de s = ⟨s₀ … s_{n-1}⟩ (noté t ⊑ s) s'il existe des indices **strictement croissants** i₁ < i₂ < … < i_k tels que s_{i_j} = t_j pour tout j. Les éléments n'ont pas besoin d'être consécutifs.
   - `ac` est-elle une sous-séquence de s ? `ca` ? `cb` ? `aa` ? `abc` ?
   - Quelle différence avec une **sous-chaîne** (*substring*) ?
2. **Plongement (sur papier).** Le tuple d'indices (i₁, …, i_k) est appelé un **plongement** (*embedding*) de t dans s.
   - Listez **tous** les plongements de `ac` dans s, puis de `abc` dans s.
   - Une sous-séquence peut avoir plusieurs plongements : lequel a le plus petit dernier indice ?
3. **Implémentation.** Écrivez :
   ```python
   def find_embedding(t, s):
       """Renvoie le plongement le plus à gauche (liste d'indices) de t dans s, ou None."""
   ```
   Utilisez une stratégie **gloutonne** (pour chaque élément de t, prenez la première occurrence possible après la précédente). Testez sur les exemples de la question 2. Quelle est la complexité de votre fonction en fonction de |s| ?
   *(Facultatif)* écrivez `all_embeddings(t, s)` et vérifiez vos réponses de la question 2.
4. **Pourquoi le plongement le plus à gauche ?** Montrez (en une ou deux phrases) que le plongement glouton a le plus petit dernier indice parmi tous les plongements. Vous en aurez besoin en Partie D.
5. **Support.** Le support d'une séquence t dans une base DB est le **nombre de séquences de DB qui contiennent t** comme sous-séquence. Une séquence compte **une seule fois**, même si t y a plusieurs plongements.
   ```python
   def support(t, db): ...
   ```
   Calculez le support de `a`, `d`, `ab`, `ba`, `ca`, `abc`, `aa` dans `DB`. Vérifiez d'abord à la main pour `ab` et `ba`.
6. **Anti-monotonie.** Comparez support(`ab`) et support(`abc`), support(`b`) et support(`ba`). Énoncez la propriété générale reliant support(t) et support(t′) lorsque t ⊑ t′. Pourquoi permet-elle de couper des branches du DFS de la Partie A ?
7. **Préfixe et suffixe.** Un **préfixe** de s est formé de ses *k* premiers éléments ; le **suffixe** correspondant est formé des éléments restants.
   - Listez tous les préfixes et tous les suffixes de s = `abcac` (y compris la séquence vide). Combien y en a-t-il ?
   - Écrivez `prefixes(s)` et `suffixes(s)` (une ligne chacune avec le découpage de listes).
   - **Lien avec les sous-séquences :** pour une sous-séquence α de s, on appelle **projection de s par rapport à α** (notée s|α) le suffixe de s qui commence **juste après le dernier indice du plongement le plus à gauche** de α. Calculez à la main s|`a`, s|`ab`, s|`ac`, s|`ca`. Écrivez la fonction `project_seq(s, alpha)` en réutilisant `find_embedding`.

### Partie D — La base projetée, pas à pas

La **base projetée** de DB par rapport à un préfixe α, notée DB|α, est la collection des projections s|α pour toutes les séquences s de DB qui **contiennent** α.

1. **Sur papier d'abord.** Avec la base jouet `DB` :
   - Construisez DB|`a`, DB|`b`, DB|`c` et DB|`d` (une ligne par séquence de DB contenant le préfixe).
   - Que se passe-t-il pour S5 = `cba` dans DB|`a` ? Le suffixe obtenu est vide : faut-il garder S5 pour calculer le **support** de `a` ? Et pour chercher les extensions de `a` ?
   - Dans DB|`a`, comptez, pour chaque item x, le **nombre de suffixes** qui contiennent x. Comparez avec support(`ax`) calculé en Partie C : que constatez-vous ?
2. **Implémentation directe.**
   ```python
   def project(db, alpha):
       """Base projetée DB|alpha : liste des suffixes s|alpha pour s contenant alpha."""
   ```
   Vérifiez vos réponses de la question 1.
3. **Projection incrémentale.** Calculez (DB|`a`)|`b` en projetant la base **déjà projetée** sur le seul item `b`, puis comparez avec DB|`ab`. Même chose avec (DB|`a`)|`c` et DB|`ac`. Que constatez-vous ? Pourquoi est-ce vrai (indice : plongement le plus à gauche, Partie C.4) ? Écrivez `project_item(db_proj, x)` qui projette sur **un seul** item.
4. **Compter dans une base projetée.**
   ```python
   def count_items(db_proj):
       """Dictionnaire item -> nombre de suffixes contenant l'item (chaque suffixe compte au plus une fois)."""
   ```
   Appliquez-la à DB|`a` et DB|`ab`. Quelles séquences de longueur 2 commençant par `a`, et de longueur 3 commençant par `ab`, ont un support ≥ 2 ?
5. **Diviser pour régner.** Expliquez en quelques phrases :
   - pourquoi l'ensemble des motifs fréquents se **partitionne** selon leur premier item (motifs commençant par `a`, par `b`, …) ;
   - pourquoi chaque partie peut être fouillée **uniquement** à partir de la base projetée correspondante ;
   - pourquoi ces bases projetées sont **plus petites** que DB (nombre de séquences et longueur).

### Partie E — Assembler : PrefixSpan, puis comparaison avec SPMF

#### E.1 — L'algorithme

1. Assemblez vos fonctions en un DFS **guidé par les données** :
   ```python
   def prefixspan(db_proj, minsup, prefix, resultats):
       # 1. compter les items dans db_proj (count_items)
       # 2. pour chaque item x fréquent (support >= minsup), dans l'ordre :
       #       - enregistrer prefix + [x] avec son support
       #       - appeler récursivement prefixspan sur project_item(db_proj, x)
       ...

   resultats = {}
   prefixspan(DB, 2, [], resultats)
   ```
   Ici `minsup` est un **support absolu** (nombre de séquences).
2. Exécutez-le sur la base jouet pour `minsup` = 1, 2, 3 et 4. Notez à chaque fois le **nombre de motifs** et la **longueur maximale** d'un motif. Recopiez la liste complète des motifs pour `minsup = 2`.
3. **Validation par force brute.** Combinez vos Parties A et C : générez toutes les séquences sur {a, b, c, d} de longueur ≤ 5 (la plus longue séquence de DB), calculez leur support, gardez celles de support ≥ `minsup`. Vérifiez par programme que vous obtenez **exactement** le même dictionnaire que PrefixSpan, pour `minsup` = 2 et 3.
4. **Retour à la Partie A.** Votre fonction `prefixspan` est un DFS, comme `dfs_naif`, mais **sans** paramètre de profondeur. Pourquoi termine-t-elle ? Donnez les deux raisons (ce qui limite la **largeur** de l'exploration, ce qui limite sa **profondeur**).

#### E.2 — Des données réelles au format SPMF

1. Lisez la documentation PrefixSpan de SPMF : <https://www.philippe-fournier-viger.com/spmf/PrefixSpan.php>. Repérez le format d'entrée (`-1` = fin d'itemset, `-2` = fin de séquence), le format de sortie (`#SUP:`), les paramètres optionnels et la commande en ligne :
   ```bash
   java -jar spmf.jar run PrefixSpan input.txt output.txt 40%
   ```
2. Écrivez la base jouet au format SPMF dans `toy.txt` (codage a=1, b=2, c=3, d=4), par exemple la première ligne : `1 -1 2 -1 3 -1 1 -1 3 -1 -2`. Lancez SPMF avec `40%` (soit 2 séquences sur 5) et vérifiez que vous retrouvez **les mêmes motifs et supports** que votre implémentation.
3. Écrivez `read_spmf(path)` qui lit un fichier de séquences SPMF en liste de listes d'entiers.
   > Les fichiers peuvent commencer par des lignes de métadonnées (`@CONVERTED_FROM_TEXT`, `@ITEM=...`, etc.) : ignorez les lignes vides et celles commençant par `@`, `#` ou `%`. Ajoutez une **vérification** qui lève une erreur si un itemset contient plus d'un item (notre implémentation ne gère que des items simples).
4. Téléchargez depuis la page des jeux de données (<https://www.philippe-fournier-viger.com/spmf/datasets.php>) les bases **SIGN** (`SIGN.txt`, langue des signes) et **LEVIATHAN** (`LEVIATHAN.txt`, le livre de Hobbes, un mot = un item). Avec votre propre code, calculez et notez pour chacune : nombre de séquences, nombre d'items distincts, longueur moyenne et longueur maximale. Comparez avec les chiffres annoncés par SPMF.

#### E.3 — Comparaison de performances avec SPMF

1. Écrivez un script qui, pour un jeu de données et une liste de valeurs de `minsup` (en %) :
   - lance **votre** PrefixSpan et mesure le temps (sans compter la lecture du fichier, que vous mesurez à part) ;
   - lance **SPMF** via `subprocess` et mesure le temps total de l'appel, **et** relève le temps affiché par SPMF dans ses statistiques ;
   - compare le **nombre de motifs** obtenus par les deux implémentations.
2. **Conversion du seuil.** SPMF prend un pourcentage ; votre fonction prend un nombre de séquences. Choisissez une conversion (arrondi supérieur ? inférieur ?) et **vérifiez** qu'elle donne le même nombre de motifs que SPMF ; si ce n'est pas le cas, testez l'autre. Notez celle qui fonctionne.
3. Valeurs indicatives (à adapter à votre machine ; partez d'un seuil élevé et descendez) :
   - *SIGN* : 70 %, 60 %, 50 %, 45 %, 40 %, 35 %, … ;
   - *LEVIATHAN* : 30 %, 20 %, 15 %, 10 %, 7 %, 5 %, ….

   Au moins **5 valeurs par jeu**, chaque mesure répétée **3 fois** (moyenne). Arrêtez de descendre dès que votre implémentation dépasse **~5 minutes** et mentionnez-le : c'est un résultat.
   > Le nombre de motifs croît très vite quand le seuil baisse. Pour de très bas seuils, SPMF peut aussi écrire un fichier de sortie énorme : surveillez l'espace disque.
4. Tracez, pour chaque jeu de données, le **temps** en fonction de `minsup` (axe des abscisses décroissant de gauche à droite ou non, mais indiquez-le) avec une courbe pour votre implémentation et une pour SPMF (échelle log en ordonnée recommandée), et ajoutez le **nombre de motifs** (second axe ou second graphique).
5. **Analyse.** Calculez le **facteur d'accélération** SPMF / votre code pour chaque seuil. Est-il constant ? Quelle part du temps SPMF mesuré par `subprocess` est due au démarrage de la JVM (comparez au temps interne de SPMF, surtout aux seuils élevés) ? Le temps suit-il le nombre de motifs ? Les deux jeux se comportent-ils de la même façon, et pourquoi (nombre d'items, longueur des séquences, densité) ?
6. **Pour aller plus loin (facultatif).** Votre `project_item` **copie** des suffixes. Implémentez une **pseudo-projection** : une base projetée est représentée par une liste de couples (indice de séquence, position de début) au lieu de copies. Mesurez l'effet sur le temps **et** sur la mémoire. Le résultat est-il celui que vous attendiez ? (Indice : en Python, le découpage de listes est exécuté en C.)

---

## 5. Livrables

### 5.1 Le rapport (en anglais)

- **Une page de contenu, rédigée en anglais** (format A4, police 11 pt minimum, marges raisonnables).
- **Jusqu'à 2 pages supplémentaires** réservées **uniquement** aux références, tableaux et figures.
- Format **PDF**, nommé `NOM_Prenom_TD_PrefixSpan.pdf` (ou `NOM1_NOM2_TD_PrefixSpan.pdf` en binôme).

**Ce qui est attendu dans la page de contenu** (à titre indicatif) :

- pourquoi le DFS naïf ne termine pas et ce qui fait de l'espace de recherche un espace de **séquences** (2–3 phrases) ;
- la formule N(m, d) et son accord (ou désaccord) avec vos mesures de la Partie B ;
- les définitions clés (sous-séquence, plongement, support, base projetée) illustrées sur **un** exemple de la base jouet ;
- en quoi PrefixSpan applique « diviser pour régner », et pourquoi il termine sans borne de profondeur ;
- comment vous avez validé votre implémentation (force brute, SPMF) ;
- l'analyse de la comparaison avec SPMF (facteur d'accélération, tendances, explication).

Une page, c'est court : privilégiez les **observations chiffrées** et les arguments. Les figures doivent être lisibles, avoir des axes titrés avec unités, une légende, et être référencées dans le texte (« see Figure 1 »).

**Dans les pages annexes :**

- Figure(s) Partie B : nombre de séquences et temps en fonction de *d* (échelle log), avec la courbe théorique ;
- Figure(s) Partie E : temps (votre code vs SPMF) et nombre de motifs en fonction de `minsup`, pour SIGN et LEVIATHAN ;
- un tableau des mesures (jeu, minsup, temps de votre code, temps SPMF interne, temps SPMF total, nombre de motifs) ;
- les caractéristiques de votre machine ;
- les références (au minimum : l'article PrefixSpan de Pei et al. et le site SPMF).

### 5.2 Le formulaire de remise

La remise se fait **exclusivement** via le Google Form : <https://forms.gle/KZup99XqLkCEXbf49>

Le formulaire contient des **questions précises** sur chaque partie (valeurs obtenues sur la base jouet, mesures, interprétations), un champ pour **coller votre code** (ou un lien vers un dépôt / notebook accessible) et un champ pour **téléverser votre rapport PDF**. Les réponses doivent être cohérentes avec votre rapport et avec ce que produit votre code.

### 5.3 Critères d'évaluation (indicatifs)

- compréhension du DFS naïf, de la borne de profondeur et de la notion de séquence
- formule, mesures, courbes et analyse
- sous-séquence, plongement, support, préfixe/suffixe (exactitude du code et des valeurs)
- base projetée (à la main et par programme), argument « diviser pour régner »
- PrefixSpan correct et validé (force brute + SPMF)
- expérimentation et analyse de la comparaison avec SPMF
- Rapport : clarté, concision, qualité de l'anglais et respect du format

---

## 6. Références

- J. Pei, J. Han, B. Mortazavi-Asl, H. Pinto, Q. Chen, U. Dayal, M.-C. Hsu, « PrefixSpan: Mining Sequential Patterns Efficiently by Prefix-Projected Pattern Growth », *Proceedings of the 17th International Conference on Data Engineering (ICDE)*, 2001, p. 215–224.
- J. Pei, J. Han, B. Mortazavi-Asl, J. Wang, H. Pinto, Q. Chen, U. Dayal, M.-C. Hsu, « Mining Sequential Patterns by Pattern-Growth: The PrefixSpan Approach », *IEEE Transactions on Knowledge and Data Engineering*, 16(11), 2004, p. 1424–1440.
- R. Agrawal, R. Srikant, « Mining Sequential Patterns », *Proceedings of the 11th International Conference on Data Engineering (ICDE)*, 1995.
- P. Fournier-Viger, J. C.-W. Lin, R. U. Kiran, Y. S. Koh, R. Thomas, « A Survey of Sequential Pattern Mining », *Data Science and Pattern Recognition*, 1(1), 2017, p. 54–77.
- P. Fournier-Viger et al., « SPMF: A Java Open-Source Pattern Mining Library », *Journal of Machine Learning Research*, 15, 2014, p. 3389–3393. Site : <https://www.philippe-fournier-viger.com/spmf/>
- Documentation SPMF — PrefixSpan : <https://www.philippe-fournier-viger.com/spmf/PrefixSpan.php>
- Jeux de données SPMF : <https://www.philippe-fournier-viger.com/spmf/datasets.php>
