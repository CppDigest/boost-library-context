# #83 Add basic CMake build system [Closed]

> Username: samcday  
> Created at: 2019-04-28 15:33:47 UTC  
> Updated at: 2019-06-07 06:06:13 UTC  
> Closed at: 2019-06-07 06:06:13 UTC  
> Url: https://github.com/boostorg/regex/pull/83  

Just directly ripping off the work I've noticed @Mike-Devel doing across a bunch of Boost modules.  
  
I determined the list of dependencies by grepping for `#include` directives. I don't know this library very well, so let me know if any of the dependencies don't make sense.  
  
This PR depends on:  
  
 * [functional](https://github.com/boostorg/functional/pull/11)

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2019-04-28 17:24:15 UTC  
> Url: https://github.com/boostorg/regex/pull/83#issuecomment-487399216  

I'd like to see a CI test for this.  Same comment for your other PR's really.

---

## Comment 2

> Username: samcday  
> Created_at: 2019-04-28 18:42:39 UTC  
> Url: https://github.com/boostorg/regex/pull/83#issuecomment-487405012  

@jzmaddock sure - how far do you want it to go with CMake? Confirming it's valid? Confirming various generators (VS201x, ninja, GNU make, etc) can init from it? Getting the actual test suite running and passing via CMake?  
  
I'm happy to look at whatever level of detail you want out of this.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2019-04-28 18:45:53 UTC  
> Url: https://github.com/boostorg/regex/pull/83#issuecomment-487405277  

I just want enough to know that the the CMake file you've added is valid - essentially that it's possible to build and use regex with it from a minimal hello-world app.

---

## Comment 4

> Username: Mike-Devel  
> Created_at: 2019-05-11 11:00:11 UTC  
> Url: https://github.com/boostorg/regex/pull/83#issuecomment-491501330  

@samcday: Are you still working in this?

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2019-05-11 11:05:54 UTC  
> Updated_at: 2019-05-11 11:07:07 UTC  
> Url: https://github.com/boostorg/regex/pull/83#issuecomment-491501675  

Btw. regex doesn't depend on functional.  The header boost/functional/hash.hpp resides (for whatever reason) in Boost.ContainerHash

---

## Comment 6

> Username: samcday  
> Created_at: 2019-06-07 06:06:13 UTC  
> Url: https://github.com/boostorg/regex/pull/83#issuecomment-499768436  

Not gonna have time to own this through to completion. Closing it out.

---
