# #24 Fixed mismatched tag warning [Closed]

> Username: Kojoley  
> Created at: 2018-11-01 00:26:37 UTC  
> Updated at: 2018-11-12 11:33:33 UTC  
> Closed at: 2018-11-12 02:46:47 UTC  
> Url: https://github.com/boostorg/parameter/pull/24  

```  
In file included from ../boost/parameter/parameters.hpp:31:  
In file included from ../boost/parameter/aux_/pack/tag_keyword_arg.hpp:9:  
In file included from ../boost/parameter/aux_/tag.hpp:8:  
../boost/parameter/aux_/tagged_argument.hpp:36:1: warning: 'tagged_argument' defined as a struct template here but previously declared as a class template [-Wmismatched-tags]  
struct tagged_argument : tagged_argument_base  
^  
../boost/parameter/aux_/tagged_argument_fwd.hpp:12:5: note: did you mean struct here?  
    class tagged_argument;  
    ^~~~~  
    struct  
```

---

## Comment 1

> Username: eldiener  
> Created_at: 2018-11-12 02:46:47 UTC  
> Url: https://github.com/boostorg/parameter/pull/24#issuecomment-437735505  

This has been changed by another PR to be consistent.

---
