# Tests techniques
## Dossier et sous dossier

Tu as une liste de fichiers et de dossiers (sous forme d’un tableau multidimensionnel). 
Chaque dossier contient un tableau de fichiers ou d’autres dossiers. Crée une fonction `getTotalSize` qui calcule la taille totale de tous les fichiers dans un dossier, y compris les sous-dossiers.

**Cas de test** :

```
$directory = [
    'name' => 'root',
    'size' => 0,
    'contents' => [
        ['name' => 'file1.txt', 'size' => 100],
        ['name' => 'file2.txt', 'size' => 200],
        [
            'name' => 'subfolder1',
            'size' => 0,
            'contents' => [
                ['name' => 'file3.txt', 'size' => 300],
                ['name' => 'file4.txt', 'size' => 400],
            ],
        ],
    ],
];
```
### Réponse

```
<?php
    function getTotalSize(array $folder): int {
        $totalSize = $folder['size'];
        
        if (isset($folder['contents'])) {
            foreach ($folder['contents'] as $item) {
                if (isset($item['contents'])) {
                    $totalSize += getTotalSize($item); // Appel récursif pour sous-dossier
                } else {
                    $totalSize += $item['size'];
                }
            }
        }

        return $totalSize;
    }

    // Test
    $directory = [
        'name' => 'root',
        'size' => 0,
        'contents' => [
            ['name' => 'file1.txt', 'size' => 100],
            ['name' => 'file2.txt', 'size' => 200],
            [
                'name' => 'subfolder1',
                'size' => 0,
                'contents' => [
                    ['name' => 'file3.txt', 'size' => 300],
                    ['name' => 'file4.txt', 'size' => 400],
                ],
            ],
        ],
    ];

    echo getTotalSize($directory); // Sortie : 1000
```

### Conseils

- Il faut connaitre la définition du principe de récursivité. Une fonction récursive est une fonction qui s'appelle elle même.

    Dans notre cas, `$directory` est une variable de type `Array`. C'est un tableau qui peut contenir d'autres tableaux et surtout qui ont la même structure.

    Cette structure nous permet donc de faire appel à la fonction `getTotalSize()` en passant en paramètre `$directory`. Lorsque cette fonction rencontrera une clé `contents` elle s'appellera elle même en passant en paramètre `$directory["content"]` et le processus recommencera à chaque fois qu'il trouvera une clé "content".

- Ici, chaque clé "content" contient un tableau de la même structure que les autres, ce qui nous permet d'envisager le récursif. Cependant, dans certains cas, il faut garder en tête que la structure peut être différente et adapter la vérification si nécessaire.

- PHP est un langage qui peut s'avérer gourmand en ressources mémoire. Avec le récursif, il faut pouvoir anticiper les actions pour ne pas facilement se retrouver dans une boucle infinie.

### Propreté du code

Un point bonus est d'expliquer pourquoi il n'y a pas de balise `?>` pour fermer le script (Il s'agit là d'une bonne pratique pour permettre à un futur collaborateur d'ajouter facilement des fonctionnalités au code, tout comme le fait d'ajouter une virgule à chaque dernier élément de chaque tableau).

## Static ou non static

Tu dois choisir la meilleure option entre deux fonctions dans un scénario donné. L’objectif est de déterminer si tu dois utiliser une méthode static ou non-static en fonction des besoins de la situation.

Pour chaque situation, donne la meilleure réponse, puis, en fonction de ta réponse, écris une version de la fonction qui fonctionne correctement dans le contexte de cette situation.

**Scénario 1 : Calcul d’une valeur partagée par toutes les instances**

Imaginons une classe qui gère des calculs de coefficients qui sont les mêmes pour toutes les instances de la classe. Cela ne dépend pas de l’état particulier de chaque objet. Tu as besoin d’une méthode qui retourne ce coefficient.

**Scénario 2 : Modification de l’état d’une instance spécifique**

Tu as une classe représentant des utilisateurs, et tu veux une méthode qui permet de modifier l’âge d’un utilisateur particulier. La méthode doit manipuler l’état de l’instance spécifique (par exemple, l’âge d’un utilisateur).

**Scénario 3 : Comportement partagé par toutes les instances, mais lié à l’objet courant**

Tu as une classe représentant des objets géométriques (par exemple, des cercles), et tu veux calculer la surface d’un cercle, mais avec la possibilité de modifier le rayon directement dans la méthode (en accédant aux propriétés de l’objet actuel).

**Question** : Devrais-tu utiliser une méthode static ou non-static pour cela ?

### Réponse
#### Scénario 1
**Static** car le coefficient est partagé par toutes les instances et ne dépend pas de l’état individuel des objets. Une méthode statique est idéale dans ce cas.

```
class MathClass {
    private static $coefficient = 1.5;

    public static function getCoefficient() {
        return self::$coefficient;
    }
}
```

#### Scénario 2
**Non-static** car elle doit manipuler des données spécifiques à l’instance, comme l’âge de l’utilisateur. Une méthode non statique a accès à $this et aux propriétés de l’objet.

```
class User {
    private $age;

    public function setAge($age) {
        $this->age = $age;
    }
}
```

#### Scénario 3
**Non-static** car elle doit accéder aux propriétés de l’objet actuel, comme le rayon d’un cercle.

```
class Circle {
    private $radius;

    public function __construct($radius) {
        $this->radius = $radius;
    }

    public function calculateArea() {
        return pi() * $this->radius ** 2;
    }
}
```

### Conseil
La différence entre une function static ou non static affecte également la gestion de la mémoire. Il est important de s'en souvenir lorsqu'il s'agira de passer à l'optimisation.

Dans notre cas, cela n'a que très peu d'importance. Dans des cas plus importants, une fonction static n'a pas besoin qu'on instancie sa classe, cela signifie donc qu'elle ne prendra pas de place en mémoire, là où une méthode non static devra avoir une classe instanciée `new MyClass()` pour fonctionner. Ce `new` créera un emplacement mémoire pour cet objet.

Appel d'une fonction non static :
```
$myClass = new MyClass();
echo $myClass->myFunction();
```

Appel d'une fonction static :
```
MyClass::myFunction();
```