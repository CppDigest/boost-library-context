# #163 - UB in big_constant.hpp and error_handling.hpp: [Closed]

> Username: NAThompson  
> Created at: 2018-11-25 00:37:53 UTC  
> Updated at: 2024-01-24 21:42:13 UTC  
> Closed at: 2024-01-24 21:42:13 UTC  
> Url: https://github.com/boostorg/math/issues/163  

Probably low priority, but some UB in `big_constant.hpp` and `error_handling.hpp`:  
  
```bash  
$  ../../../b2 -a --reconfigure cxxflags="--std=c++14 -fsanitize=address -fsanitize=undefined" linkflags="-fsanitize=address -fsanitize=undefined" test_2F0  
../../../boost/math/tools/big_constant.hpp:43:26: runtime error: -2.79443e+39 is outside the range of representable values of type 'float'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/math/tools/big_constant.hpp:43:26 in  
../../../boost/math/policies/error_handling.hpp:803:26: runtime error: -2.79443e+39 is outside the range of representable values of type 'float'  
SUMMARY: UndefinedBehaviorSanitizer: undefined-behavior ../../../boost/math/policies/error_handling.hpp:803:26 in  
```
