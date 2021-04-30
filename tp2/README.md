# TP2 : Le tri Boustrophédon

## Pseudo-code

```t
procédure boustrophédon(in_out t : tableau[1..n] de élément; i,j : 1..n)
début
    tantque i < j faire
        pour k de i à (j - 1) avec un pas de 1 faire
            si t[k] > t[k + 1] alors
                permuter(t[k], t[k + 1]);
            finsi;
        finpour;
        j := j - 1;

        pour k de j à i avec un pas de -1 faire
            si t[k] < t[k - 1] alors
                permuter(t[k], t[k - 1])
        finpour;
        i := i + 1;
    fintantque;
fin;
```

## Sémantique axiomatique

```t
{∀ k de 1 à i-1, t[k] >= t[k - 1] ∧ ∀ l de j à n-1, t[l] <= t[l+1]}
tant que i < j faire
    {(∀ k de 1 à i-1, t[k] >= t[k - 1] ∧ ∀ l de j+1 à n, t[l] <= t[l+1]) ∧ (i < j)}

    
    {(∀ k de 1 à i-1, t[k] >= t[k - 1] ∧ ∀ l de j+1 à n, t[l] <= t[l+1])}
fintantque;
{(i >= j) ∧ (∀ k de 1 à i-1, t[k] >= t[k - 1] ∧ ∀ l de j+1 à n, t[l] <= t[l+1])} => {∀ k de 1 à n - 1, t[k] <= t[k + 1]}
```

## C++

```c++
#include <vector>
#include <cassert>
#include <algorithm>

template<typename T>
void permuter(std::vector<T> &t, int i, int j) {
    T buf = t[i];
    t[i] = t[j];
    t[j] = buf;
}

template<typename T>
void boustrophedon(std::vector<T> &t, int i, int j) {
    // ===== Assertion
    for (int k = 1; k < i - 1; ++k) { // ∀ k de 1 à i-1, t[k] >= t[k - 1]
        assert(t[k] >= t[k - 1]);
    }
    for (int l = j; l < t.size() - 1; ++l) { // ∀ l de j à n-1, t[l] <= t[l+1]
        assert(t[l] <= t[l + 1]);
    }
    // =====

    while (i < j) {
        // ===== Assertion
        for (int k = 1; k < i - 1; ++k) { // ∀ k de 1 à i-1, t[k] >= t[k - 1]
            assert(t[k] >= t[k - 1]);
        }
        for (int l = j; l < t.size() - 1; ++l) { // ∀ l de j à n-1, t[l] <= t[l+1]
            assert(t[l] <= t[l + 1]);
        }
        assert(i < j); // i < j
        // =====

        for (int k = i; k < j; k++) {
            if (t[k] > t[k + 1]) {
                permuter(t, k, k + 1);
            }
        }
        j--;

        for (int k = j; k > i; k--) {
            if (t[k] < t[k - 1]) {
                permuter(t, k, k - 1);
            }
        }
        i++;

        // ===== Assertion
        for (int k = 1; k < i - 1; ++k) { // ∀ k de 1 à i-1, t[k] >= t[k - 1]
            assert(t[k] >= t[k - 1]);
        }
        for (int l = j; l < t.size() - 1; ++l) { // ∀ l de j à n-1, t[l] <= t[l+1]
            assert(t[l] <= t[l + 1]);
        }
        // =====
    }

    // ===== Assertion
    assert(i >= j); // i >= j
    for (int k = 1; k < i - 1; ++k) { // ∀ k de 1 à i-1, t[k] >= t[k - 1]
        assert(t[k] >= t[k - 1]);
    }
    for (int l = j; l < t.size() - 1; ++l) { // ∀ l de j à n-1, t[l] <= t[l+1]
        assert(t[l] <= t[l + 1]);
    }
    // =>
    assert(std::is_sorted(t.begin(), t.end()));
    // =====
}
```
