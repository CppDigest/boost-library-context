# #507 - Circular dependency of boost/hana/concept/struct.hpp and boost/hana/accessors.hpp breaks tooling [Open]

> Username: braxtons12  
> Created at: 2022-08-04 21:47:14 UTC  
> Updated at: 2022-08-04 21:47:14 UTC  
> Url: https://github.com/boostorg/hana/issues/507  

Title says it all  
  
`<boost/hana/concept/struct.hpp>` includes `<boost/hana/accessors.hpp>`... which includes `<boost/hana/concept/struct.hpp>`.  
  
Is this intentional? It doesn't cause build errors (at least on linux, GCC/Clang), but it breaks tooling like clangd and clang-tidy
