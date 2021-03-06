# TP1 : Sémantique axiomatique de la procédure partition

## Sémantique axiomatique

```t
procédure partition(in_out t : tableau[1..n] de élément; i,j : 1..n; out k : 1..n);
var
    l = 1..n
début
    permuter(t[i], median(t[i], t[j], t[(i + j) div 2]));
    l := i + 1;
    k := j;
    # 1 {t[j] >= t[i] >= t[(i+j)/2] v t[j] <= t[i] <= t[(i+j)/2]}
    tantque l <= k faire
        tantque t[k] > t[i] et l <= k faire
            # 2 {∀ e ϵ ]k, j] ; t[e] > t[i]}
            k := k - 1;
        fintantque;

        tantque t[l] <= t[i] et l <= k faire
            # 3 {∀ a ϵ [i, l[ ; t[a] <= t[i]}
            l := l + 1;
        fintantque;

        si l < k alors
            permuter(t[l], t[k])
            l := l + 1;
            k := k - 1;
        finsi;
        # 4 {∀ e ϵ ]k, j] ; ∀ a ϵ [i, l[ ; t[e] > t[i] ^ t[a] <= t[i]}
    fintantque;
    # 5 {∀ e ϵ ]k, j] ; ∀ a ϵ [i, l[ ; t[e] > t[i] ^ t[a] <= t[i] ^ l > k}
    permuter(t[i], t[k])
    # 6 {∀ e ϵ [i, k] ; ∀ a ϵ [k, j] ; t[e] <= t[k] ^ t[a] >= t[k]}
fin;
```

## Implémentation

```c++
#include <iostream>
#include <vector>
#include <algorithm>
#include <cassert>
#include <random>

template<typename T>
void permuter(T &a, T &b) {
    T t = a;
    a = b;
    b = t;
}

template<typename T>
unsigned int indiceMedian(std::vector<T> &t, int x, int y, int z) {
    if ((t[x] <= t[y] && t[x] >= t[z]) || (t[x] >= t[y] && t[x] <= t[z])) {
        return x;
    } else if ((t[y] <= t[x] and t[y] >= t[z]) or (t[y] >= t[x] and t[y] <= t[z])) {
        return y;
    } else {
        return z;
    }
}

template<typename T>
void partition(std::vector<T> &t, int i, int j, int m) {
    permuter(t[i], t[m]);
    int l = i + 1;
    int k = j;

    // ===== Assertion 1
    // {t[j] >= t[i] >= t[(i+j)/2] v t[j] <= t[i] <= t[(i+j)/2]}
    assert((t[j] >= t[i] && t[i] >= t[(i + j) / 2]) ||
           (t[j] <= t[i] && t[i] <= t[(i + j) / 2]));
    // =====

    while (l <= k) {
        while (l <= k && t[k] > t[i]) {
            // ===== Assertion 2
            // {∀ e ϵ ]k, j] ; t[e] > t[i]}
            for (int e = k + 1; e <= j; ++e) {
                assert(t[e] > t[i]);
            }
            // =====
            k--;
        }

        while (l <= k && t[l] <= t[i]) {
            // ===== Assertion 3
            // {∀ a ϵ [i, l[ ; t[a] <= t[i]}
            for (int a = i; a < l; ++a) {
                assert(t[a] <= t[i]);
            }
            // =====
            l++;
        }

        if (l < k) {
            permuter(t[l], t[k]);
            l++;
            k--;
        }
        // ===== Assertion 4
        // {∀ e ϵ ]k, j] ; ∀ a ϵ [i, l[ ; t[e] > t[i] ^ t[a] <= t[i]}
        for (int e = k + 1; e <= j; ++e) {
            assert(t[e] > t[i]);
        }
        for (int a = i; a < l; ++a) {
            assert(t[a] <= t[i]);
        }
        // =====
    }
    // ===== Assertion 5
    // {∀ e ϵ ]k, j] ; ∀ a ϵ [i, l[ ; t[e] > t[i] ^ t[a] <= t[i] ^ l > k}
    assert(l > k);
    for (int e = k + 1; e <= j; ++e) {
        assert(t[e] > t[i]);
    }
    for (int a = i; a < l; ++a) {
        assert(t[a] <= t[i]);
    }
    // =====

    permuter(t[i], t[k]);
    // ===== Assertion 6
    // {∀ e ϵ [i, k] ; ∀ a ϵ [k, j] ; t[e] <= t[k] ^ t[a] >= t[k]}
    for (int e = i; e <= k; e++) {
        assert(t[e] <= t[k]);
    }
    for (int a = k; a <= j; a++) {
        assert(t[a] >= t[k]);
    }
    // =====
}
```
