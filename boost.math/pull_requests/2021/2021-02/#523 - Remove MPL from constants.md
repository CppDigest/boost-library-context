# #523 Remove MPL from constants [Merged]

> Username: mborland  
> Created at: 2021-02-07 14:57:45 UTC  
> Updated at: 2021-02-09 18:02:23 UTC  
> Merged at: 2021-02-08 08:47:02 UTC  
> Closed at: 2021-02-08 08:47:02 UTC  
> Url: https://github.com/boostorg/math/pull/523  

Remove MPL, and other C++11 changes.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2021-02-07 18:51:30 UTC  
> Url: https://github.com/boostorg/math/pull/523#issuecomment-774732008  

We probably should remove use of boost::integral_constant, but it will need a coordinated change inside Multiprecision.

---

## Comment 2

> Username: mborland  
> Created_at: 2021-02-07 19:05:45 UTC  
> Url: https://github.com/boostorg/math/pull/523#issuecomment-774736910  

I will open a PR in multiprecision to make the change, and put `std::integral_constant` back in here.

---

## Comment 3

> Username: mborland  
> Created_at: 2021-02-07 19:53:13 UTC  
> Url: https://github.com/boostorg/math/pull/523#issuecomment-774747620  

Both this and https://github.com/boostorg/multiprecision/pull/294 replace `boost::integral_constant` with `std::integral_constant` now.

---

## Comment 4

> Username: jzmaddock  
> Created_at: 2021-02-08 08:47:34 UTC  
> Url: https://github.com/boostorg/math/pull/523#issuecomment-774979641  

Merged, both this and Multiprecision will be shown as failing until the boost super-project updates.

---
