# #117 test.hpp: Suppress Clang -Wignored-qualifiers warning [Merged]

> Username: Kojoley  
> Created at: 2019-03-14 21:32:24 UTC  
> Updated at: 2019-03-16 11:31:28 UTC  
> Merged at: 2019-03-16 08:50:49 UTC  
> Closed at: 2019-03-16 08:50:49 UTC  
> Url: https://github.com/boostorg/type_traits/pull/117  

```  
../libs/type_traits/test/test.hpp:221:9: warning: 'const' qualifier on reference type 'r_type' (aka 'int &') has no effect [-Wignored-qualifiers]  
```  
The warning is enabled on default error level.

---
