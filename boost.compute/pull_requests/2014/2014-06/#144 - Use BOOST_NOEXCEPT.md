# #144 Use BOOST_NOEXCEPT [Merged]

> Username: jamboree  
> Created at: 2014-06-19 10:08:57 UTC  
> Updated at: 2014-06-19 15:33:17 UTC  
> Merged at: 2014-06-19 15:33:13 UTC  
> Closed at: 2014-06-19 15:33:13 UTC  
> Url: https://github.com/boostorg/compute/pull/144  

Replace `noexcept` with `BOOST_NOEXCEPT` to support pre-c++11 compilers.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-06-19 10:48:12 UTC  
> Url: https://github.com/boostorg/compute/pull/144#issuecomment-46546500  

[![Coverage Status](https://coveralls.io/builds/883355/badge)](https://coveralls.io/builds/883355)  
  
Coverage remained the same when pulling **c518869dd47c6f52a9438b70894f1bd3a069759d on jamboree:develop** into **b670212ee8dc0a4cdd2fcffa90f423ee43221aad on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-06-19 15:15:29 UTC  
> Url: https://github.com/boostorg/compute/pull/144#issuecomment-46573712  

Are there compilers which support rvalue-references and `std::move()` but don't support `noexcept`?

---

## Comment 3

> Username: jamboree  
> Created_at: 2014-06-19 15:22:05 UTC  
> Url: https://github.com/boostorg/compute/pull/144#issuecomment-46574617  

msvc10

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-06-19 15:33:17 UTC  
> Url: https://github.com/boostorg/compute/pull/144#issuecomment-46576192  

Works for me. Thanks!

---
