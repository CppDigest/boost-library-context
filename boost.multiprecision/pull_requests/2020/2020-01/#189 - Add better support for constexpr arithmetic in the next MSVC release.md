# #189 Add better support for constexpr arithmetic in the next MSVC release. [Merged]

> Username: jzmaddock  
> Created at: 2020-01-29 18:51:32 UTC  
> Updated at: 2021-01-30 16:09:59 UTC  
> Merged at: 2020-01-30 12:09:53 UTC  
> Closed at: 2020-01-30 12:09:53 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/189  



---

## Comment 1

> Username: pabristow  
> Created_at: 2020-01-30 13:02:38 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/189#issuecomment-580242218  

Is this expected to work for recent Clang 9 that I think I remember has std:is_const_evaluated?  
  
(Although I've stopped trying to use constexpr with cpp_bin_float as promote_args does not seem to be constexpr-able, so not trying this at present).

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-01-30 17:32:17 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/189#issuecomment-580366646  

clang-9 was already supported.  This is for a yet-to-be-released MSVC.  cpp_bin_float currently has no constexpr support (I haven't got that far yet).

---
