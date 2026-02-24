# #119 C5240: 'maybe_unused' [Open]

> Username: chrisse74  
> Created at: 2023-12-21 07:47:45 UTC  
> Updated at: 2023-12-21 07:47:45 UTC  
> Url: https://github.com/boostorg/phoenix/pull/119  

Fixes Windows Visual Studio 2022 (17.8.2) warning C5240: 'maybe_unused' attribute is ignored in this syntactic position  
  
When including the generated header files we see many 'maybe_unused' compiler warnings, e.g.:  
`boost/phoenix/core/detail/cpp03/preprocessed/argument_predefined_10.hpp(21): warning C5240: 'maybe_unused': attribute is ignored in this syntactic position`  
  
  
  
Be warned: I fixed these manually - maybe there is a generating mechanism involving 'BOOST_PHOENIX_ARGUMENT_N_INSTANCE' that I dont know of. But this works as intended in our code base.

---
