# TP3 : Type Graphe

## Type Graphe

### Structure 1

Cette première structure utilise 2 listes, une pour les noeuds et une pour les arcs. Le graphe est orienté mais du fait de la liste d'arcs il est tout à fait possible de prendre les arcs à l'envers.

```t
sorte liste(sorte t);
avec entier, booléen;
constante εliste;
opérations
    -[-] : liste(t) × entier → t;
    - = εliste : liste(t) → booléen;
    ajouter : liste(t) × t → liste(t);
    enlever : liste(t) × t → liste(t);
    taille : liste(t) → entier;
    contient : liste(t) × t → booléen;
avec
    l : liste;
    e : entier;
    x : t;
préconditions
    enlever(l) est-défini-ssi (l = εliste) = faux;
    l[e] est-défini-ssi (l = εliste) = faux;
axiomes
    ajouter(l, x) ⇒ l[taille(l)] = x;
    (εliste = εliste) = vrai;
    (ajouter(l, x) = εliste) = faux;
    taille(ajouter(l, x)) = taille(l) + 1;
    taille(enlever(l, x)) = taille(l) - 1;
    contient(ajouter(l, x), x) = vrai;
fin_sorte;
```

```t
sorte noeud(sorte t);
opérations
    valeur : noeud(t) → t;
    valeur : noeud(t) × t → noeud(t);
avec
    n1, n2 : noeud(t); 
    x : t;
axiomes
    valeur(valeur(n1, x)) = x;
    valeur(n1, x) = valeur(n2, x);
fin_sorte;
```

```t
sorte arc;
utilise noeud, entier;
opérations
    de : arc → noeud;
    de : arc × noeud → arc;
    à : arc → noeud;
    à : arc × noeud → arc;
    poids : arc → entier;
    poids : arc × entier → arc;
avec
    a : arc;
    n : noeud;
    e : entier;
axiomes
    de(de(a, n)) = n;
    à(à(a, n)) = n;
    poids(poids(a, e)) = e;
fin_sorte;
```

```t
sorte graphe(sorte t);
utilise noeud(t), arc, liste;
opérations
    noeuds : graphe(t) → liste(noeud(t));
    arcs : graphe(t) → liste(arc);
    dijkstra : graphe(t) × noeud(t) × noeud(t) → liste(noeud(t));
avec
    g : graphe;
    n, n2 : noeud;
préconditions
    dijkstra(g, n, n2) est-défini-ssi contient(noeuds(g), n) = contient(noeuds(g), n2) = vrai;
fin_sorte;
```

### Structure 2

La seconde structure n'utilise qu'une liste de noeuds, c'est ensuite chaque noeud qui contient une liste de noeuds associés avec un poids.
On réutilise la spécification de liste de la première structure.

```t
sorte paire(sorte k, sorte v);
opérations
    clé : paire(k, v) → k;
    clé : paire(k, v) × k → paire(k, v);
    valeur : paire(k, v) → v;
    valeur : paire(k, v) × v → paire(k, v);
avec
    p : paire;
    cl : k;
    vl : v;
axiomes
    clé(clé(p, cl)) = cl;
    valeur(valeur(p, vl)) = vl;
fin_sorte;
```

```t
sorte noeud(sorte t);
utilise liste, paire, entier;
opérations
    suivants : noeud(t) → liste(paire(noeud, entier));
    valeur : noeud(t) → t;
    valeur : noeud(t) × t → noeud(t);
avec
    n1, n2 : noeud(t);
    x : t;
axiomes
    valeur(valeur(n1, x)) = x;
    valeur(n1, x) = valeur(n2, x);
fin_sorte;
```

```t
sorte graphe(t);
utilise liste, noeud;
opérations
    noeuds : graphe(t) → liste(noeud(t));
    dijkstra : graphe(t) × noeud(t) × noeud(t) → liste(noeud(t));
avec
    g : graphe;
    n, n2 : noeud;
préconditions
    dijkstra(g, n, n2) est-défini-ssi contient(noeuds(g), n) = contient(noeuds(g), n2) = vrai;
find_sorte;
```

## Implémentations diskstra

### Type 1

### Type 2
