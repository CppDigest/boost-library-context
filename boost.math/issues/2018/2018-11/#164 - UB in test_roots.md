# #164 - UB in test_roots [Closed]

> Username: NAThompson  
> Created at: 2018-11-25 02:11:55 UTC  
> Updated at: 2018-12-01 11:21:45 UTC  
> Closed at: 2018-12-01 11:21:45 UTC  
> Url: https://github.com/boostorg/math/issues/164  

To reproduce:  
  
```bash  
$  ../../../b2 -a --reconfigure cxxflags="--std=gnu++17 -fsanitize=address -fsanitize=undefined" linkflags="-fsanitize=address -fsanitize=undefined" test_roots  
====== BEGIN OUTPUT ======  
../../../boost/math/tools/roots.hpp:439:137: runtime error: division by zero  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/math/tools/roots.hpp:439:137 in  
../../../boost/math/tools/roots.hpp:439:137: runtime error: division by zero  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/math/tools/roots.hpp:439:137 in  
```
