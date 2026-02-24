# #120 - C5240: 'maybe_unused': attribute is ignored in this syntactic position [Open]

> Username: chrisse74  
> Created at: 2023-12-21 09:14:57 UTC  
> Updated at: 2023-12-21 09:14:57 UTC  
> Url: https://github.com/boostorg/phoenix/issues/120  

When including the generated header files we see many 'maybe_unused' compiler warnings in Windows Visual Studio 2022 (17.8.2), e.g.:  
`boost/phoenix/core/detail/cpp03/preprocessed/argument_predefined_10.hpp(21): warning C5240: 'maybe_unused': attribute is ignored in this syntactic position`  
  
Could be fixed by pull request #119
