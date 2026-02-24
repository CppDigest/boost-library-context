# #543 Make expression templates implicitly convert... [Merged]

> Username: jzmaddock  
> Created at: 2023-03-11 18:37:52 UTC  
> Updated at: 2023-03-24 18:47:56 UTC  
> Merged at: 2023-03-13 18:27:24 UTC  
> Closed at: 2023-03-13 18:27:24 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/543  

to types that the number type will also convert to. Fixes: https://github.com/boostorg/multiprecision/issues/541.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2023-03-13 18:26:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/543#issuecomment-1466692508  

Only failure is a network issue, merging.

---

## Comment 2

> Username: afabri  
> Created_at: 2023-03-22 12:38:43 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/543#issuecomment-1479500350  

Is this already in boost 1.82 ?

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2023-03-22 17:47:11 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/543#issuecomment-1480007536  

It is, however, this https://github.com/boostorg/multiprecision/pull/545 has already cropped up, and as I fear this is the tip of an iceberg shaped tricky problem, I'm contemplating pulling the fix from 1.82.

---

## Comment 4

> Username: lrineau  
> Created_at: 2023-03-23 17:14:02 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/543#issuecomment-1481575350  

This code does not compile with Boost-1.82 but compiled with previous versions. Is that related?  
```c++  
#include <boost/multiprecision/cpp_int.hpp>  
  
using Rational = boost::multiprecision::cpp_rational;  
  
template <typename N> struct Sqrt {  
  Sqrt() {}  
  Sqrt(N) {}  
  friend Sqrt<N> operator*(const Sqrt<N>& x, const Sqrt<N>&) { return x;}  
  friend Sqrt<N> operator*(const N&, const Sqrt<N>& x) { return x;}  
};  
  
int main() {  
  Rational r;  
  Sqrt<Rational> s;  
  
  Rational(r - r) * s;  
  Sqrt<Rational> toto(r - r);  
  Sqrt<Rational>(r - r) * s;  
  (r - r) * s;  // ambiguous call to `operator*` here  
}  
```

---

## Comment 5

> Username: jzmaddock  
> Created_at: 2023-03-23 17:49:31 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/543#issuecomment-1481629511  

Yes, confirmed.  
  
I've asked for permission to pull this fix from 1.82.  It may even be that the issue is basically unfixable :(

---

## Comment 6

> Username: afabri  
> Created_at: 2023-03-24 07:32:18 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/543#issuecomment-1482373128  

It works for `mpq_class` which (we should double check) also uses expression templates.

---

## Comment 7

> Username: lrineau  
> Created_at: 2023-03-24 16:00:16 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/543#issuecomment-1483043986  

I agree this PR should be reverted. The class `mpq_class` from `<gmpxx.h>` does not has those `operator T()` to convert expression to everything. Instead their equivalent of `number` has non-explicit conversion constructors from the expression templates.  
  
Since this PR, Boost MP expression templates are convertible to too many types, and that can lead to compilation errors with ambiguous calls (like https://github.com/boostorg/multiprecision/pull/543#issuecomment-1481575350).

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2023-03-24 18:47:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/543#issuecomment-1483261968  

Reverted in develop and master.

---
