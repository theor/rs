## Résumé 2p

## Intro 10p max
###  Sujet
###  Présentation entreprise 3p
###  Relations/connaissances sujet rapport à l'entreprise
###  Rapports sujet/cursus, motivations à faire le stage
###  Intérêt du stage pour l'entreprise
###  Contexte du stage
    doc, aide, ...

## Aspects organisationnels 25p
###  étapes, délivrables, processus, gantt, ...
###  Respect des délais
###  Points de contrôle, maitre de stage, ...
###  Crises
  
## Aspects techniques : 40p
A – Présentation du ou des objectif(s) 
B – Des alternatives possibles (a justifier) 
C – Le cadre imposé par l’entreprise 
D – Vos propositions retenues ou pas  
E – Les difficultés éventuelles 
F – Le ou les résultats obtenus (s) ou le niveau d’avancement ainsi que l’impact sur le sujet de stage et votre investissement  

### EnumCustomization

Parmi les types de données définis en C++ dans le moteur du jeu, beaucoup sont utilisés dans l'éditeur : une entité qui possède une position sera déplacable via Anvil, un matériau modifiable via l'éditeur correspondant, etc.

Afin de permettre la communication entre le moteur en c++ et l'éditeur en C#, un protocole réseau, Arrow, a été développé. Mais une fois les données reçues côté C#, il faut encore les rendre utilisables, à savoir en transformant ces données brutes en objects .NET.

Cette transformation est automatisée, mais repose sur l'existence de types de données .NET correspondant aux types C++. Ces classes, écrites en C#, sont générés à partir de fichiers MOLD.
Un fichier MOLD est associé à un type C++ et décrit quelles propriétés et méthodes de ce type exposer côté C# : les membres de la classe générée dépendent donc du contenu du fichier MOLD.

L'un des problèmes récurrents des programmeurs outils est de minimiser la nécessité de leur présence lors de modifications mineures et/ou cosmétique. Par exemple, le nom affiché d'un plugin, la documentation d'une commande, ... Pour pallier ce problème, il est possible de spécifier des attributs sur les membres des classes exposés via le fichier MOLD. Une propriété "quatRot" pourra se voir affecter un nom humainement lisible "Rotation", une description "Rotation de l'entité, sous forme de quaternion", une unité, une visibilité par rôle (Animateur, Programmeur, Game Designer, ...), etc.

Ce comportement a été implémenté via les OverridableProperties, une classe représentant une propriété dont la valeur est au départ celle spécifiée dans le code, mais qui peut être écrasée via une interface dédiée dans l'éditeur. De cette manière, la présence des développeurs outils n'est pas requise pour renommer une classe, une commande, ...

Ma mission a consisté à implémenter un comportement similaire sur les énumérations définies dans le moteur. Les enums possédaient déjà des équivalents C# générés ; le travail s'est axé autour de deux points, à savoir l'implémentation d'overridable properties pour les descripteurs d'énumérations et la réalisation de l'interface de personnalisation correspondante.

Une classe représentant une énumération C++ existait déja dans l'éditeur. L'implémentation des propriétés a été rapide, le code étant très similaire à ce qui existait déjà pour les classes. L'interface, de la même facon, est elle aussi calquée sur l'existant.

L'interface résultante est l'exemple parfait d'une situation propice à l'utilisation de la virtualisation offerte par WPF :

- Il y a un nombre conséquent d'éléments
- Chaque ligne a la même hauteur
- Le groupement d'éléments est désactivés
- La visibilité des éléments est constant : pas de réduction/agrandissment

De fait, la virtualisation permet de gagner en vitesse de chargement et d'affichage en réduisant le nombre d'éléments visuels instanciés au nombre maximal d'éléments affichables en même temps plus quatre.

Enfin, il faut sauvegarder les propriétés écrasées. Les autres outils de personnalisation sérialisent les données nécessaires au format XML avant de les sauvegarder dans GuildLib, la base de données du Pipeline. Afin de ne pas rompre l'uniformité, la même technique a été utilisée.

Ces modifications ont été reprises par une autre équipe de développement utilisant le même pipeline, ce qui permet de considérer la mission comme un succès.

### Property Browser

L'objectif primaire d'Anvil est de permettre la modification et la sauvegarde d'objets et de leur propriétés. Dans ce but, une grille de propriétés (éponymement nommée "Property Grid") est disponible dans l'éditeur.

Cette grille a aujourd'hui plusieurs inconvénients :

- Programmée en WinForms, le code et l'interface souffrent d'un couplage fort
- L'interface elle-même est figée, le thème de couleurs étant difficilement modifiable avec cette technologie
- Les objets contenant très souvent d'autres objets, le résultat est un arbre de profondeur problématique
- L'affichage des propriétés est fortement couplé à la déclarations des classes correspondantes côté moteur

Afin de pallier ces points noirs, le *Property Browser* ("Explorateur de propriétés") a été créé par le Technology Group, afin d'offrir un contrôle générique intégrable à n'importe quel Pipeline.

Pour atteindre cet objectif, l'architecture de l'outil est articulée comme suit :

- Un contrôle WPF contenant principalement une liste d'éléments, les *Property Items*
- Un *Property Provider* responsable de l'extraction des propriétés d'un objet et de la création des Property Items correspondants
- Une collection de classes dérivées de Property Item, et les fichiers de ressources correspondants décrivant l'interface, spécialisés pour un type d'éditeur de propriété : par exemple, l'éditeur de nombre, de matrices ou d'images.

Une implémentation adaptée à Anvil et Scimitar existait déjà à mon arrivée, sur une branche expérimentale du Techonology Group. Malheuresement, celle-ci était incomplète au vu de la Property Grid existante et en moyenne trois fois plus lente à extraire les propriétés d'un objet.

L'objectif de la mission est donc double : répliquer la Property Grid existante en terme de performances et de fonctionnalités.

Le Property Browser faisant partie d'Alexandrie, la base de code commune à plusieurs projets développée par l'équipe du Technology Group, toutes les fonctionnalités et optimisations qui ne sont pas spécifiques au Pipelin d'Assassin's Creed doivent répondre à des critères stricts de généricités.

#### Intégration Scimitar

La première étape a donc consisté à intégrer le Property Browser dans la branche principale du projet.  Si l'intégration de la partie commune a été relativement triviale, l'implémentation ciblant le pipeline Anvil/Scimitar se basait, elle, sur une branche dérivée il y a plus de deux ans de celle utilisée pour Assassin's Creed. De fait, les divergences de code se sont multipliées au gré des refactorings des deux côtés.

- AC1
-- AC2
--- AC3
-- Osborne
--- Alexandrie

Une fois le contrôle intégré, une application de démonstration a été développée afin de vérifier son bon fonctionnement et permettre de développer et prototyper rapidement de nouvelles fonctionnalités.

[controls demo]

La phase suivante, l'intégration de l'implémentation Scimitar, a nécessité de se plonger dans les arcanes de l'éditeur et du moteur.

En effet, l'édition de propriétés d'objets du moteur repose sur plusieurs pilliers :

- Les fichiers de définitions MOLD
- Les wrappers générés en C#
- Le protocole de communication réseau Arrow
- le framework de customization décrit précédemment

Les définitions MOLD ont pour objectif, premièrement, de distinguer les propriétés visibles et modifiables depuis le property browser. Une série d'attributs permet de spécifier le comportement du Browser envers cette propriété : par exemple, indiquer qu'une propriété est une distance ou un volume, ...

Des wrappers sont générés à la compilation, à savoir des classes C# correspondantes aux classes C++, dont le détail est régulé par les MOLD.

Le protocole de communication *Arrow* est basé sur Tcp/Ip et permet la transmission simple de paquets générés depuis le moteur ou l'éditeur. Ce système a été amélioré jusqu'à permettre la synchronisation d'un objet Scimitar et de sa représentation wrappée en C#. Les interactions entre l'éditeur et le moteur utilisent toutes ce protocole.

Le framework de customization permet, ici, de modifier le nom affiché d'une propriété ou de la documenter ; par exemple, une propriété nommée "GRotRad" sera affichée sous le nom de "Global Rotation (in Radians)".

Optimisation WPF et virtualisation
Refactoring et flattening
Prop Commands
- Cmd system
- Refactoring

Unit types
- Orig Unit
- Samples