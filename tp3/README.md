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

```c++
#include <vector>
#include <map>
#include <climits>
#include <algorithm>

// Classe Noeud générique
template<typename T>
class Noeud {
private:
    // Valeur du noeud
    T valeur;

public:
    Noeud() = default;

    explicit Noeud(T valeur) : valeur(valeur) {}

    Noeud(Noeud<T> const &noeud) {
        valeur = noeud.valeur;
    }

    T getValeur() const {
        return valeur;
    }
};

// Classe Arc
template<typename T>
class Arc {
private:
    // L'arc va de ce noeud
    Noeud<T> de;
    // à celui là
    Noeud<T> a;
    // et possède un poids, une distance
    unsigned int poids;

public:
    Arc(const Noeud<T> &de, const Noeud<T> &a, unsigned int poids) : de(de), a(a), poids(poids) {}

    Noeud<T> getDe() const {
        return de;
    }

    Noeud<T> getA() const {
        return a;
    }

    unsigned int getPoids() const {
        return poids;
    }
};

// Classe Graphe
template<typename T>
class Graphe {
private:
    // Les noeuds qui le compose
    std::vector<Noeud<T>> noeuds;
    // Les arcs qui relient les noeuds
    std::vector<Arc<T>> arcs;

public:
    Graphe() {
        noeuds = std::vector<Noeud<T>>();
        arcs = std::vector<Arc<T>>();
    }

    std::vector<Noeud<T>> getNoeuds() {
        return noeuds;
    }

    std::vector<Arc<T>> getArcs() {
        return arcs;
    }

    void setNoeuds(const std::vector<Noeud<T>> &pNoeuds) {
        Graphe::noeuds = pNoeuds;
    }

    void setArcs(const std::vector<Arc<T>> &pArcs) {
        Graphe::arcs = pArcs;
    }

    // Renvoie le chemin entre debut et fin
    std::vector<Noeud<T>> dijkstra(Noeud<T> debut, Noeud<T> fin) {
        // Tableau des distances
        std::map<Noeud<T>, unsigned int> distances;
        // Tableau des prédecesseurs
        std::map<Noeud<T>, Noeud<T>> pred;
        // Sous-graphe
        std::vector<Noeud<T>> q = noeuds;

        // On initialise le tableau distance avec que des infini
        for (auto noeud:noeuds) {
            distances.emplace(noeud, UINT_MAX);
        }
        // La distance entre début et début vaut 0
        distances[debut] = 0;

        // Tant qu'il reste des noeuds non explorés
        while (!q.empty()) {
            // On récupère le noeud le plus proche en question de poids
            unsigned int mini = UINT_MAX;
            Noeud<T> n1;
            for (auto noeud:q) {
                if (distances[noeud] < mini) {
                    mini = distances[noeud];
                    n1 = noeud;
                }
            }
            // On enlève ce noeud du sous-graphe
            q.erase(std::find(q.begin(), q.end(), n1));

            // Pour tout les noeuds suivant n1
            for (Arc<T> arc:arcs) {
                if (arc.getDe() == n1) {
                    // Si la distance entre début et le n2 et plus grande que celle de début à n1 + le poids de l'arc
                    if (distances[arc.getA()] > (distances[n1] + arc.getPoids())) {
                        // On met à jour la distance
                        distances[arc.getA()] = distances[n1] + arc.getPoids();
                        // Et on note ou on est passé
                        pred[arc.getA()] = n1;
                    }
                }
            }
        }

        // On récupère le chemin de début à fin en remontant les prédécesseurs depuis fin
        std::vector<Noeud<T>> resultat;
        Noeud<T> n = fin;
        while (n != debut) {
            resultat.push_back(n);
            n = pred[n];
        }
        resultat.push_back(debut);
        std::reverse(resultat.begin(), resultat.end());

        return resultat;
    }
};

template<typename T>
std::ostream &operator<<(std::ostream &flux, Noeud<T> n) {
    flux << n.getValeur();
    return flux;
}

template<typename T>
bool operator==(Noeud<T> n1, Noeud<T> n2) {
    return n1.getValeur() == n2.getValeur();
}

template<typename T>
bool operator!=(Noeud<T> n1, Noeud<T> n2) {
    return !(n1 == n2);
}

template<typename T>
bool operator<(Noeud<T> n1, Noeud<T> n2) {
    return n1.getValeur() < n2.getValeur();
}

template<typename T>
bool operator>(Noeud<T> n1, Noeud<T> n2) {
    return n1.getValeur() > n2.getValeur();
}

template<typename T>
bool operator<=(Noeud<T> n1, Noeud<T> n2) {
    return (n1 == n2) || (n1 < n2);
}

template<typename T>
bool operator>=(Noeud<T> n1, Noeud<T> n2) {
    return (n1 == n2) || (n1 > n2);
}
```

### Type 2

```c++
#include <vector>
#include <map>
#include <climits>
#include <algorithm>

// Classe Noeud générique
template<typename T>
class Noeud {
private:
    // Valeur du noeud
    T valeur;
    // Les noeuds suivant
    std::map<Noeud<T>, unsigned int> suivants;

public:
    Noeud() = default;

    explicit Noeud(T valeur) : valeur(valeur) {}

    Noeud(T valeur, const std::map<Noeud<T>, unsigned int> &suivants) : valeur(valeur), suivants(suivants) {}

    Noeud(Noeud<T> const &noeud) {
        valeur = noeud.valeur;
        suivants = noeud.suivants;
    }

    T getValeur() const {
        return valeur;
    }

    std::map<Noeud<T>, unsigned int> getSuivants() {
        return suivants;
    }

    void setSuivants(const std::map<Noeud<T>, unsigned int> &pSuivants) {
        Noeud::suivants = pSuivants;
    }
};

// Classe Graphe
template<typename T>
class Graphe {
private:
    // Les noeuds qui le compose
    std::vector<Noeud<T>> noeuds;

public:
    std::vector<Noeud<T>> getNoeuds() {
        return noeuds;
    }

    void setNoeuds(const std::vector<Noeud<T>> &pNoeuds) {
        Graphe::noeuds = pNoeuds;
    }

    // Renvoie le chemin entre debut et fin
    std::vector<Noeud<T>> dijkstra(Noeud<T> debut, Noeud<T> fin) {
        // Tableau des distances
        std::map<Noeud<T>, unsigned int> distances;
        // Tableau des prédecesseurs
        std::map<Noeud<T>, Noeud<T>> pred;
        // Sous-graphe
        std::vector<Noeud<T>> q = noeuds;

        // On initialise le tableau distance avec que des infini
        for (auto noeud:noeuds) {
            distances.emplace(noeud, UINT_MAX);
        }
        // La distance entre début et début vaut 0
        distances[debut] = 0;

        // Tant qu'il reste des noeuds non explorés
        while (!q.empty()) {
            // On récupère le noeud le plus proche en question de poids
            unsigned int mini = UINT_MAX;
            Noeud<T> n1;
            for (auto noeud:q) {
                if (distances[noeud] < mini) {
                    mini = distances[noeud];
                    n1 = noeud;
                }
            }
            // On enlève ce noeud du sous-graphe
            q.erase(std::find(q.begin(), q.end(), n1));

            // Pour tout les noeuds suivant n1
            for (auto n2:n1.getSuivants()) {
                // Si la distance entre début et le n2 et plus grande que celle de début à n1 + le poids de l'arc
                if (distances[n2.first] > distances[n1] + n2.second) {
                    // On met à jour la distance
                    distances[n2.first] = distances[n1] + n2.second;
                    // Et on note ou on est passé
                    pred[n2.first] = n1;
                }
            }
        }

        // On récupère le chemin de début à fin en remontant les prédécesseurs depuis fin
        std::vector<Noeud<T>> resultat;
        Noeud<T> n = fin;
        while (n != debut) {
            resultat.push_back(n);
            n = pred[n];
        }
        resultat.push_back(debut);
        std::reverse(resultat.begin(), resultat.end());

        return resultat;
    }
};

template<typename T>
std::ostream &operator<<(std::ostream &flux, Noeud<T> n) {
    flux << n.getValeur();
    return flux;
}

template<typename T>
bool operator==(Noeud<T> n1, Noeud<T> n2) {
    return n1.getValeur() == n2.getValeur();
}

template<typename T>
bool operator!=(Noeud<T> n1, Noeud<T> n2) {
    return !(n1 == n2);
}

template<typename T>
bool operator<(Noeud<T> n1, Noeud<T> n2) {
    return n1.getValeur() < n2.getValeur();
}

template<typename T>
bool operator>(Noeud<T> n1, Noeud<T> n2) {
    return n1.getValeur() > n2.getValeur();
}

template<typename T>
bool operator<=(Noeud<T> n1, Noeud<T> n2) {
    return (n1 == n2) || (n1 < n2);
}

template<typename T>
bool operator>=(Noeud<T> n1, Noeud<T> n2) {
    return (n1 == n2) || (n1 > n2);
}
```
