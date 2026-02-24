# #20 - symbol already defined [Closed]

> Username: jcfly  
> Created at: 2018-09-12 02:38:13 UTC  
> Updated at: 2018-09-18 17:35:20 UTC  
> Closed at: 2018-09-18 17:35:19 UTC  
> Url: https://github.com/boostorg/dll/issues/20  

non template free functions in dll/detail/demangling/msvc.hpp are not inline,  so they cannot be used by multiple cpp files, or there will be a link error "symbol already defined"

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-09-18 17:35:19 UTC  
> Url: https://github.com/boostorg/dll/issues/20#issuecomment-422481848  

Added fixes and test. Test passes on Linux and Windows. All the changes will be merged to master soon
