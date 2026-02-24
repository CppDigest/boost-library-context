# #131 Add basic CMake build system [Closed]

> Username: samcday  
> Created at: 2019-04-28 16:01:29 UTC  
> Updated at: 2019-06-07 06:06:41 UTC  
> Closed at: 2019-06-07 06:06:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/131  

Just directly ripping off the work I've noticed @Mike-Devel doing across a bunch of Boost modules.  
  
I determined the list of dependencies by grepping for `#include` directives. I don't know this library very well, so let me know if any of the dependencies don't make sense.  
  
This PR depends on:  
  
 * [functional](https://github.com/boostorg/functional/pull/11)  
 * [lexical_cast](https://github.com/boostorg/lexical_cast/pull/26)  
 * [math](https://github.com/boostorg/math/pull/199) (cyclic!)  
 * [random](https://github.com/boostorg/random/pull/55) (cyclic!)

---
