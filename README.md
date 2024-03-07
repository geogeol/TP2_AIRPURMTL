# TP2_AIRPURMTL
TP2_versionfinale
Au cours de ce projet, on a travaillé avec des données en temps réel sur la qualité de l'air (IQA). Les données IQA sont répertorié dans plusieurs jeux de données dans _données ouvertes Montréal._ On a décidé de garder les jeux de données qui représentent les valeurs de IQA en temps réel avec les stations ouvertes, les polluants la date, l’heure et les coordonnées en X,Y et long, lat ; et le jeu de données qui regroupe plus d’informations notamment l’état de stations (ouvert/fermé), l' arrondissement dans lequel les stations se trouvent.

|   |                                                                                                                                                                                                   |
| - | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|   |                                                                                                                                                                                                   |
|   | ![](https://lh7-us.googleusercontent.com/gDwh0kw_hLtu1hIyCpDOAlJSZfC4BFRjXcVA_2sZmJe0LmcVs_s8p3apKKHLCPPfkcSw43VDcTpysLJb0o-TQB60b7__5o1hOH55ABLpUkhLSCGiWEmKJ5zUKDQt3p4id8ohmrvrVpJeGGVrNy6U9ug) |

&#x20;Il a fallu d’abord nettoyer le tableau de données IQA en modifiant et en corrigeant les noms des stations, ainsi qu'en les organisant de manière logique, classée par ordre de 1 à 11 et nettoyée en supprimant les stations fermées. Cette tâche fût réalisée à l'aide des transformateurs FME tels que _StringReplacer_ pour renommer les attributs et le transformer _Sorter_ pour organiser les stations par numéro de manière exhaustive :

 

Nous avons travaillé ensuite les données IQA temps réelle où nous avons nettoyé, notamment renommé les stations pour une meilleure compréhension. Nous avons créé des entités avec _Vertexcreator_ puis nous l’avons projeté avec _Esriprojector_ dans le système de projection géographique WGS84 Mercator pour pouvoir l’assimiler à une jointure spatiale via _Pointonareaoverlayer_, qui n’a pas abouti :![](https://lh7-us.googleusercontent.com/zq4Og_x038lZNpP2OMBSlTqvdrhgpOoWDv9oDk9-ZEhBUQ7WswylzLr0w11KokGazgooy9eZkiORiQDnWB6eDug0GJYJ71j1HdTDlvwlyGCj1zspaz4XvFtos4INedbxUgSQ_TG3hZROh57oUo7Lw9c)

 En effet, nous voulions joindre spatialement les stations IQA avec une grille H3 indexée sur la ville de Montréal. Nous avons donc utilisé le transformer _H3haxognaleindexer_ pour créer un pattern hexagonale de la ville (0.2Km) afin d’assimiler à la fois les valeur de IQA et les valeurs du nombre de véhicules à moteurs par quartier :![](https://lh7-us.googleusercontent.com/SGXJGsfXEMCSVA0okHrVBYqUs_pI8GnP7qB4Yz_sUuC7_fT-zyVGiRb8WJIosTQH_9nnX2yzZYAW59MEjVYSk8bja9p7G8-6c-hHk1oVDbKAgg7YNmOscfVazCflttwAx1s6HDhvqCU68EoCMboKzNc)

L’étape qui nous a demandé le plus de temps c’est celle du nettoyage des données TR. En effet, les données brut ont été compliquées à traiter étant donné que les date à laquelle le comptage de véhicules réalisé sont des attributs d’entrée. De ce fait, étant donné que le jeu de données est assez lourd, cela nous a impactés pour son traitement. Il est composé d’énormément de données car le jeu de données considéré les villes de Calgary, Toronto, Ontario, en plus de celle de Montréal. De plus, pour de nombreuses dates, il n’y avait pas d’enregistrement de véhicules aux intersections de rue ce qui rendait le jeu de données incomplet. L’idée d’un traitement statistique était de remplacer les valeurs \<null> par la médiane, plus représentatif que la moyenne (pour chaque jour) calculée à l’aide du transformer _StaistiqueCalculator_. Mais nous ne sommes pas parvenus à remplacer les valeurs \<null> par leur médiane respective.

Nous ne sommes pas parvenu à remodeler le tableau de jeu de données et faire un tableau avec ces attributs en entrée :![](https://lh7-us.googleusercontent.com/_MyRKz533yqdAiaEOghF-6WSxJ8q1KNlhJk5dg17GYzNtT5l7sd41BrahqDcxjgu7bruqbycmzU_hf98RbT83gnTgzypLpc1rF6WxViLoJaD057u16cV9AtJL1xWSCZCsYLm4gr-QgTd-UZ1i3ogWQU)\
&#x20;De plus, nous avons réussi, tardivement dans le processus, à extraire les coordonnées spatiales de ce jeu de données car elle était dans un format spécifique : WKT. Il a fallu utiliser le transformer _Geometryreplacer_ pour extraire les coordonnées issu de l’attribut \<position>. Nous avons également voulu joindre spatialement les positions des caméras à la grille H3 mais nous obtenons le même résultat qu’avec les données IQA, non concluant.  ![](https://lh7-us.googleusercontent.com/bR8j6eGamDYAJhRUl2yhXG88JrI83tAk7-1RXRxlEdCM6E7ANLTN-VXsi5r_gHRbVCDMrGo14i7qpWuinDUHDNs8VYuoS09fTAtK2iU1XJawagyDiLSyCZUhOjT2cEy5vmPzNFJ1Mo6AhVkAdU-WbFw)

Or, pour travailler au mieux avec les données de station de la qualité de l’air dans QGIS notre volonté était de les transformer dans FME en données raster pour que chaque hexagone de la grille h3 comporte une donnée matricielle avec la valeur de l’indice de la qualité de l’air. Même chose pour le trafic routier mais c’est une manipulation que nous ne sommes pas parvenu à réaliser.

Nous avons intégré le jeu de données des parc de Montréal ou nous filtré les types de parc et d’espaces vert pour garder des espaces vert libre de droit d’accès, soit public, municipale etc, puis enregistré dans la base de données, avec le système de projection EPSG3857\
&#x20;adapté :![](https://lh7-us.googleusercontent.com/afj1E93Prr7Ehgg4hxkYmmRgiWxvfklshsys7zYUCYj4NN1rlvabJz_jQ_nH8IHcfqJG6c3PAdIL4jdjB-BaEF8LRc0-QA2UaZ-64cji6ry-KzXq7hgPUaoblvN2EJ5C1rV4Fqn4aF1Pr8MLA2H3QQw)

Ainsi, nous obtenons seulement des couches vectorielles sur QGIS lorsqu’on visualise les données, après les avoir reprojetées dans le même système de projection à savoir EPSG :3857 dans FME. Nous avons la couche des limites administratives de Montréal, la grille h3 de la ville, les secteurs « rsqa » de Montréal, les espaces verts (parc), les caméras ponctuelles qui ont enregistré le trafic routier et la localisation des stations IQA (11).

Nous avons rencontré de nombreux problèmes lors de ce TP2. De manière générale, une mauvaise gestion du temps de traitement des jeux de données dû à un manque de connaissance du logiciel ainsi qu’une difficulté à travailler les données correctement ont porté préjudice directement sur la qualité des données en sortie et mène à une visualisation géographique non aboutie. Nous nous sommes notamment trop attardé sur le traitement des jeu de données de la qualité de l’air des stations IQA (qui semblent dire la même information) qui en vain n’était pas nécessaire pour la visualité géospatiale que l’on souhaite.
