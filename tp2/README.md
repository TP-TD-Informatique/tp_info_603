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
{∀ k de 1 à i-1, t[k] < t[k - 1] ∧ ∀ l de j à n-1, t[l] < t[l+1]}
tant que i < j faire
    {(∀ k de 1 à i-1, t[k] < t[k - 1] ∧ ∀ l de j+1 à n, t[l] < t[l+1]) ∧ (i < j)}

    
    {(∀ k de 1 à i-1, t[k] < t[k - 1] ∧ ∀ l de j+1 à n, t[l] < t[l+1])}
fintantque;
{(i >= j) ∧ (∀ k de 1 à i-1, t[k] < t[k - 1] ∧ ∀ l de j+1 à n, t[l] < t[l+1])} => {∀ k de 1 à n-1, t[k] < t[k+1]}
```

## C++

```c++
#include <vector>

template<typename T>
void permuter(T &a, T &b) {
    T t = a;
    a = b;
    b = t;
}

template<typename T>
void boustrophedon(std::vector<T> &t, unsigned int i, unsigned int j) {
    while (i < j) {
        for (unsigned int k = i; k < j; k++)
            if (t[k] > t[k + 1])
                permuter(t[k], t[k + 1]);
        j--;

        for (unsigned int k = j; k > i; k--)
            if (t[k] < t[k - 1])
                permuter(t[k], t[k - 1]);
        i++;
    }
}
```

## Extra : Fil

```kotlin
fun permuter(a: Any, b: Any) {
    val t = a
    a = b
    b = t
}

fun boustrophedon(t: List<Any>, i: int, j: int) {
    while (i < j) {
        for (k in i until j) {
            if (t[k] > t[k + 1]) {
                permuter(t[k], t[k + 1])
            }
        }
        j--

        for (k in j downTo i) {
            if (t[k] < t[k - 1]) {
                permuter(t[k], t[k - 1])
            }
        }
        i++
    }
}
```
