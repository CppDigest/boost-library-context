# #13 Add basic CMake build system [Closed]

> Username: samcday  
> Created at: 2019-04-28 15:24:11 UTC  
> Updated at: 2019-06-07 06:05:57 UTC  
> Closed at: 2019-06-07 06:05:57 UTC  
> Url: https://github.com/boostorg/unordered/pull/13  

Just directly ripping off the work I've noticed @Mike-Devel doing across a bunch of Boost modules.  
  
I determined the list of dependencies by grepping for `#include` directives. I don't know this library very well, so let me know if any of the dependencies don't make sense.  
  
This PR depends on:  
  
 * [functional](https://github.com/boostorg/functional/pull/11)

---
