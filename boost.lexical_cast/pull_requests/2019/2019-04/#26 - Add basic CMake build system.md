# #26 Add basic CMake build system [Merged]

> Username: samcday  
> Created at: 2019-04-28 15:53:06 UTC  
> Updated at: 2019-04-30 06:31:20 UTC  
> Merged at: 2019-04-30 06:31:05 UTC  
> Closed at: 2019-04-30 06:31:06 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/26  

Just directly ripping off the work I've noticed @Mike-Devel doing across a bunch of Boost modules.  
  
I determined the list of dependencies by grepping for `#include` directives. I don't know this library very well, so let me know if any of the dependencies don't make sense.  
  
This PR depends on:  
  
 * math (cyclic!)  
 * [numeric_conversion](https://github.com/boostorg/numeric_conversion/pull/17)  
 * [range](https://github.com/boostorg/range/pull/92)

---

## Comment 1

> Username: apolukhin  
> Created_at: 2019-04-30 06:31:20 UTC  
> Url: https://github.com/boostorg/lexical_cast/pull/26#issuecomment-487834343  

Many thanks for the PR!

---
