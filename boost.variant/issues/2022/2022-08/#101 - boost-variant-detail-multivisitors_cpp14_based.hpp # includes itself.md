# #101 - boost/variant/detail/multivisitors_cpp14_based.hpp # includes itself [Closed]

> Username: urnathan  
> Created at: 2022-08-29 18:57:51 UTC  
> Updated at: 2022-09-01 08:22:54 UTC  
> Closed at: 2022-09-01 08:22:42 UTC  
> Url: https://github.com/boostorg/variant/issues/101  

working on using clang modules (as a proxy for C++20 header units), I found that boost/variant/detail/multivisitors_cpp14_based.hpp includes itself.  That seems unnatural :)  
  
[boost-includes.txt](https://github.com/boostorg/variant/files/9447173/boost-includes.txt)

---

## Comment 1

> Username: apolukhin  
> Created at: 2022-09-01 08:22:54 UTC  
> Url: https://github.com/boostorg/variant/issues/101#issuecomment-1233924620  

Many thanks for the report!
