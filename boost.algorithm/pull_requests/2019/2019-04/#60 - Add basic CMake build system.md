# #60 Add basic CMake build system [Closed]

> Username: samcday  
> Created at: 2019-04-28 15:39:19 UTC  
> Updated at: 2019-06-07 06:06:19 UTC  
> Closed at: 2019-06-07 06:06:19 UTC  
> Url: https://github.com/boostorg/algorithm/pull/60  

Just directly ripping off the work I've noticed @Mike-Devel doing across a bunch of Boost modules.  
  
I determined the list of dependencies by grepping for `#include` directives. I don't know this library very well, so let me know if any of the dependencies don't make sense.  
  
This PR depends on:  
  
 * [range](https://github.com/boostorg/range/pull/92) (cyclic!)  
 * [regex](https://github.com/boostorg/regex/pull/83)  
 * [unordered](https://github.com/boostorg/unordered/pull/13)

---

## Comment 1

> Username: mclow  
> Created_at: 2019-05-03 16:30:06 UTC  
> Url: https://github.com/boostorg/algorithm/pull/60#issuecomment-489158141  

What is the deal with all the CI failures?

---

## Comment 2

> Username: Mike-Devel  
> Created_at: 2019-05-03 20:34:04 UTC  
> Updated_at: 2019-06-06 06:49:53 UTC  
> Url: https://github.com/boostorg/algorithm/pull/60#issuecomment-489231115  

Seem to be unrelated to this PR. AFAIK the cmake file is not even used in the CI

---

## Comment 3

> Username: samcday  
> Created_at: 2019-06-07 06:06:19 UTC  
> Url: https://github.com/boostorg/algorithm/pull/60#issuecomment-499768450  

Not gonna have time to own this through to completion. Closing it out.

---
