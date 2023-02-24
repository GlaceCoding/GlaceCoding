# Gestion de la balle pong en lancé de rayon

Cette petite explication explique la théorie de comment l'affichage de la balle et la gestion de collision
peut-être fait de façon lancé de rayon #miniRT.

Les principaux avantages de cette solution sont que cette solution est très légère pour le serveur car :
 - Il n'y aura qu'un seul setTimeout qui aura lieu quand la balle sera sur l'axe des Y de la raquette adverse ;
 - La position de la balle est calculé et envoyé **une seule fois** sauf si changement trajectoire, c'est-à-dire
 au début du match et lorsqu'il y a collision avec la raquette adverse.
 - Le nombre de calcul de collision est très faible, car on calcule la collision seulement avec l'axe Y des raquettes
 selon une équation mathématique. La *collision* avec les murs est seulement réalisé grâce à la position des Y est seulement ajusté
 avec un modulo pour la faire entrée à nouveau dans le terrain).

Cette solution à le plus faible usage de setTimeout possible (tout en gardant la vérification/anti-cheat côté serveur)
et le plus faible nombre de websocket envoyée possible. Si vous avez un bon netcode/synchro, vous n'aurez aucune latence
avec l'affichage de la balle.

## Introduction

Cette méthode consiste à définir la trajectoire de la balle à un instant T, cette trajectoire est définie par la
position, la direction et le timestamp de cet instant. La trajectoire est représenté par une droite, on considère
qu'il y aura collision seulement sur l'axe Y de la raquette des deux joueurs, on ne prend pas en compte les murs. Il est
important de comprendre le point suivant: on ne considère aucune collision avec les murs. La collision avec les murs
n'est qu'une question d'affichage, car le fait de toucher un mur n'affecte en rien le déroulement du jeu, il y a
qu'une seule issue anticipable : la balle va rebondir avec un angle inverse. Contrairement à la collision avec la
raquette du joueur, où on ne peut pas anticiper si la raquette sera là ou non, et va donc créer un événement avec
deux issues. Les deux issues sont : donner un point, ou collision de la balle avec la raquette. L'issue n'est donc
pas anticipable à coup sûr ce qui nous fera recalculer la trajectoire de la balle.

Cette méthode admet deux choses :

 - L'affichage recalculera la position de la balle à partir de cet instant T, en fonction du temps écoulé.
 - La fonction handler de collision est planifié dans le temps dès que la trajectoire de la balle est calculé.

*Je suppose que le terrain est de taille (400; 300) tout au long de l'explication.*

## Le calcul de la trajectoire de la balle à un instant T

La trajectoire est représentée mathématiquement par une fonction de type :

    vec(x,y) = vec(pos_x,pos_y) + vec(dir_x,dir_y) * delta_time * ball_speed

Le calcul de la trajectoire de la balle est calculé :
 - Lors du début du match ;
 - La collision de la balle avec la raquette du joueur adverse ;
 - Lorsque la trajectoire est infini ou que le trajet dépasse un certain temps. Danc mon cas, j'ai choisi 20sec.

Une trajectoire est dite infini quand l'angle d'impact du mur est de 90°, c'est-à-dire perpendiculaire à celui ci,
car la balle va faire des vas et viens sur l'axe Y, à l'infini sans aucune issue possible.

Pour calculer la trajectoire, il faut connaitre la position, la direction et le timestamp actuel. Cette trajectoire
vous permettra à chaque tour d'animation (cad à chaque appel de la fonction passé en argument de `requestAnimationFrame`),
de calculer la position de la balle, en récupérant simplement le timestamp du moment actuel, ce qui vous permettra
de calculer le delta_time :

```
delta_time = NOW - time_T
```

## La *collision* avec les murs

Le mot *collision* est inexacte car à aucun moment cette méthode va calculer la collision avec les murs, on ne prend
en compte seulement l'axe des Y de la raquette du joueur. La *collision* ou gestion des murs, est un simple
post-retraitement de la balle sur l'axe des Y.

Nous avons dis sur les murs dans l'introduction :

> La collision avec les murs n'est qu'une question d'affichage, car le fait de toucher un mur n'affecte en rien le
> déroulement du jeu, il y a qu'une seule issue anticipable : la balle va rebondir avec un angle inverse. La collision
> avec les murs n'est qu'une question d'affichage, car le fait de toucher un mur n'affecte en rien le déroulement du jeu,
> il y a qu'une seule issue anticipable : la balle va rebondir avec un angle inverse. Contrairement à la collision avec la
> raquette du joueur …

Il faut donc imaginer que le terrain se répète à l'infini en hauteur comme le schema ci-dessous.

<details>
  <summary>Cliquer pour afficher le schema sur la trajectoire de la balle</summary>
  
  Vous voyez donc le terrain se repeter à l'infini en Y, et la trajectoire de la balle (droite verte).
  
  <img width="200" src="https://user-images.githubusercontent.com/92152391/221143349-deb08eb2-cd04-4ce6-ba31-88162f0fd310.png" />
</details>

Avec cette méthode de calcul, l'affichage de la balle après la *"collision"* avec un mur, est un mirroring (effet mirroir) possible
par l'utilisation d'un modulo de la hauteur du terrain sur la position en Y de la balle, pour savoir si le résultat du modulo doit-être
inversé il faut diviser la position en Y de la balle par la hauteur du terrain, le résultat obtenu en fonction de s'il est pair ou impair
(modulo de 2 => 0 ou 1) indique s'il faut inverser le résultat obtenu par le modulo ou non.

Avec ces explications, vous devriez être capable de comprendre ce schéma :

![Schema des explications](https://user-images.githubusercontent.com/92152391/221137129-1a1d88e4-8b01-4331-bd2e-24815a152565.png)

Quelques explications supplémentaire sur le schéma :

 - La droite verte est une fonction (équation) du type `vec(x,y) = vec(pos_x,pos_y) + vec(dir_x,dir_y) * delta_time * ball_speed`
 - La droite rouge est le mirroring en Y (retraitement de la position Y sur l'affichage)
 - Seule la collision tout à droite est calculée, son résultat permet de connaitre le temps nécessaire pour atteindre le point de collision

## Calcul de colision

<details>
  <summary>Cliquer pour afficher l'indice sur la formule de math pour savoir s'il y a une collision</summary>
 
  Cherchez comment calculer une collision entre deux droites (vecteurs) avec le produit scalaire. Ceci vous permettra de savoir s'il y a une collision et
  connaitre le temps restant avant la collision. N'oubliez pas de normaliser. ;)
</details>
