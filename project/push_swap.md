# PUSH_SWAP

## Les attentes du projet

Les objectifs du projets sont :
 - Avoir *strictement* moins de 3 mouvements avec 3 valeurs ;
 - Avoir moins de 700 mouvements avec 100 valeurs pour avoir 5 (sinon pour 4/3/2/1 points : 900/1 100/1 300/15 000 mouvements);
 - Avoir moins de 5 500 mouvements avec 500 valeurs pour avoir 5 (sinon pour 4/3/2/1 points : 7 000/8 500/10 000/11 500 mouvements).

## L'algo

Les résultat obtenus par mon algo (sur 50 essais) :
 - Moyenne de 1 mouvement (1 à 2 mouvements) pour une suite de 3 valeurs ;
 - Moyenne de 610 mouvements (ne dépasse pas les 680 mouvements).
 - Moyenne **haute** de 5 465 mouvements (ne dépasse pas les 5 680 mouvements).

Pour la faible complexité de l'algo (conception et compréhension), les résultats sont très bon, c'est pourquoi je le conseille.

### Principes

Le premier concept est de ne pas utiliser **swap** (sa/sb/ss) pour trier, vous allez multiplier par 2 vos nombres de mouvement
(voir l'explication dans la partie FAQ). Vous avez besoin de **sa** seulement dans certaine configuration de votre liste de 3 valeurs, donc
**sa** doit apparaitre une ou zéro fois.

Les grandes lignes de mon algo sont :

 1. Tout push dans B, tant que le stack_a à une taille strictement supérieur à 3.
 2. Trier ma liste de 3 élements avec l'algo simple swap (qui doit faire au maximum 2 mouvements). 
 3. **Tant que le stack_b n'est pas vide :**  
 Calculer le coup le moins gourmant en mouvement (**rotate**/**reverse rotate**), pour chaque élément calculer le nombre de mouvement nécessaire
 pour vous positionner dans le stack_a et dans le stack_b. Si vous allez dans la même direction dans les deux stacks, n'oubliez pas d'utiliser
 **rr** et **rrr**, puis de **ra/rrb** ou **rb/rra** la différence. Réfléchissez aussi si vous devez **rotate** ou **reverse rotate** pour
 économiser des actions.  
 Puis push dans A et recommencer cette opération jusqu'à ce que le stack_b soit vide.
 4. **rotate** ou **reverse rotate** jusqu'au début de la liste.
 5. Print et free.
 
### Visualisation
 
Vous pouvez visualiser votre projet avec : https://github.com/o-reo/push_swap_visualizer

![Animation montrant un swap d'une liste de 100](https://raw.githubusercontent.com/GlaceCoding/GlaceCoding/main/project/res/Animation.gif)

## Tester

Je vous conseille mon fork (branch #new-options) qui rajoute plusieurs commandes : https://github.com/GlaceCoding/push_swap_tester/tree/new-options
à l'heure où j'écris [ma PR n'a pas encore été merge](https://github.com/lmalki-h/push_swap_tester/pull/10).

```
USAGE
./push_swap_tester.sh [directory-to-push_swap] [stacksize 0R range] [nb_of_test] {options}

OPTIONS
  --show-arg    Display arguments after the number of instructions.
  --quiet       Don't display arguments if the tester catch an error.
  --retry       Retry with same arguments during the last run or the specified run with --retry=[NUM].
  --score       Show the score of the current entries, useful to compare output of two differents push_swap algo.
  --bench       Use with --score, save the score in push_swap_benchmark.log, if is a new record or a new entries.
                Use --rewrite-bench to erase saved score by the current score.
  --show-index  Display sorted index of each arguments, the index is the offset position when the list is sorted.
  --help/-h     Show this message.
```

Je pense avoir ajouté des commandes plutôt sympa pour le debug notamment le `--show-arg`, `--retry` et `--score` pour pouvoir comparé vos modifications
entre deux exécutions et voir si vous êtes sur la bonne voie.

## FAQ

### Pourquoi **swap** n'est-il pas rentable ?

L'action **swap** (sa/sb/ss) va déplacer/tranvaser les deux premiers élements de votre liste mais ne va pas changer la position actuelle dans votre liste,
si vous voulez changer de position comme aller à l'élément suivant, vous allez devoir faire un **rotate** ce qui va compter comme une deuxième opération.

Alors que l'action **push** déplace un élément dans l'autre stack et change votre position dans le stack car un élément a été retiré donc vous allez avoir
automatiquement l'élément suivant en début de stack, vous allez donc faire une opération de moins en utilisant **push** plutôt que **swap**.
