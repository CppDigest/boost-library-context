# #78 - The apply_permutation algorithm requires C++11 [Closed]

> Username: eldiener  
> Created at: 2020-09-12 17:42:17 UTC  
> Updated at: 2020-11-29 21:17:52 UTC  
> Closed at: 2020-11-29 21:17:52 UTC  
> Url: https://github.com/boostorg/algorithm/issues/78  

The apply_permutation algorithm requires C++11. Is this intended ?   
  
If so, should the apply_permutation test only run when the compiler is at the C++11 or higher level ?  
If not so, should apply_permutation be changed to use Boost type traits rather than standard type traits, and will it work that way ?

---

## Comment 1

> Username: mclow  
> Created at: 2020-11-18 16:52:18 UTC  
> Url: https://github.com/boostorg/algorithm/issues/78#issuecomment-729809309  

As far as I know, this has not ben true since October 2018. (commit b6c04d6dc52ace864bb1f5cd26b65f9b639a39b9)

---

## Comment 2

> Username: eldiener  
> Created at: 2020-11-18 17:49:30 UTC  
> Url: https://github.com/boostorg/algorithm/issues/78#issuecomment-729847529  

See https://github.com/boostorg/algorithm/pull/82.

---

## Comment 3

> Username: ksoni-1258  
> Created at: 2020-11-29 06:55:52 UTC  
> Url: https://github.com/boostorg/algorithm/issues/78#issuecomment-735352818  

what is the issue,could you please elaborate

---

## Comment 4

> Username: eldiener  
> Created at: 2020-11-29 21:17:52 UTC  
> Url: https://github.com/boostorg/algorithm/issues/78#issuecomment-735455207  

It has been fixed. The issue is that the header file included <type_traits>, which is a C++11 header file.
