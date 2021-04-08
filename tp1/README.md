# TP1 : Sémantique axiomatique de la procédure partition

```t
procédure partition(in_out t : tableau[1..n] de élément; i,j : 1..n; out k : 1..n);
var
    l = 1..n
début
    permuter(t[i], median(t[i], t[j], t[(i + j) div 2]));
    l := i + 1;
    k := j;
    # 1
    tantque l <= k faire
        tantque t[k] > t[i] et l <= k faire
            # 2
            k := k - 1;
        fintantque;

        tantque t[l] <= t[i] et l <= k faire
            # 3
            l := l + 1;
        fintantque;

        si l < k alors
            permuter(t[l], t[k])
            l := l + 1;
            k := k - 1;
        finsi;
        # 4
    fintantque;
    # 5
    permuter(t[i], t[k])
    # 6
fin;
```
