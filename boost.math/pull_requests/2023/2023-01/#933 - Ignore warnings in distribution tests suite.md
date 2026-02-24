# #933 Ignore warnings in distribution tests suite [Merged]

> Username: mborland  
> Created at: 2023-01-29 19:27:46 UTC  
> Updated at: 2023-01-30 18:37:37 UTC  
> Merged at: 2023-01-30 18:37:33 UTC  
> Closed at: 2023-01-30 18:37:33 UTC  
> Url: https://github.com/boostorg/math/pull/933  

Adds:   
`-Wunused-local-typedef` in compile tests for Clang to match GCC  
`-Wdeprecated-declarations` for all compilers in complex tests that use the now deprecate complex functions  
`-Wliteral-range` for tests that use table data that truncates to zero

---
