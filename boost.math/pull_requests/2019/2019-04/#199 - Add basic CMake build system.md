# #199 Add basic CMake build system [Closed]

> Username: samcday  
> Created at: 2019-04-28 15:58:23 UTC  
> Updated at: 2019-06-07 06:06:34 UTC  
> Closed at: 2019-06-07 06:06:34 UTC  
> Url: https://github.com/boostorg/math/pull/199  

Just directly ripping off the work I've noticed @Mike-Devel doing across a bunch of Boost modules.  
  
I determined the list of dependencies by grepping for `#include` directives. I don't know this library very well, so let me know if any of the dependencies don't make sense.  
  
This PR depends on:  
  
 * [lambda](https://github.com/boostorg/lambda/pull/15)  
 * [lexical_cast](https://github.com/boostorg/lexical_cast/pull/26)  
 * [multiprecision](https://github.com/boostorg/multiprecision/pull/131) (cyclic!)

---
