# #92 Add basic CMake build system [Closed]

> Username: samcday  
> Created at: 2019-04-28 15:42:49 UTC  
> Updated at: 2019-06-07 08:37:52 UTC  
> Closed at: 2019-06-07 06:04:23 UTC  
> Url: https://github.com/boostorg/range/pull/92  

Just directly ripping off the work I've noticed @Mike-Devel doing across a bunch of Boost modules.  
  
I determined the list of dependencies by grepping for `#include` directives. I don't know this library very well, so let me know if any of the dependencies don't make sense.  
  
This PR depends on:  
  
 * [algorithm](https://github.com/boostorg/algorithm/pull/60) (cyclic!)  
 * [numeric_conversion](https://github.com/boostorg/numeric_conversion/pull/17)  
 * [regex](https://github.com/boostorg/regex/pull/83)  
 * [unordered](https://github.com/boostorg/unordered/pull/13)

---

## Comment 1

> Username: glenfe  
> Created_at: 2019-04-29 00:52:17 UTC  
> Url: https://github.com/boostorg/range/pull/92#issuecomment-487429911  

> algorithm (cyclic!)  
  
No; Boost.Range does not depend on Boost.Algorithm anymore. (Since 77a4dd51242f1ef446acb1a476ae1c760c206b1d)

---

## Review 2 [Commented]

> Username: glenfe  
> Created_at: 2019-04-29 21:55:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/range/pull/92#pullrequestreview-231922172  

📁 CMakeLists.txt

```diff
  22 |+         Boost::core
  23 |+         Boost::detail
  24 |+         Boost::functional
```

> Username: glenfe  
> Created_at: 2019-04-29 21:55:47 UTC  
> Updated_at: 2019-04-29 21:55:48 UTC  
> Url: https://github.com/boostorg/range/pull/92#discussion_r279555049  

Why is functional in there?


---

## Comment 3

> Username: Mike-Devel  
> Created_at: 2019-06-07 06:01:22 UTC  
> Url: https://github.com/boostorg/range/pull/92#issuecomment-499767468  

@samcday: Are you still working on this?

---

## Comment 4

> Username: samcday  
> Created_at: 2019-06-07 06:04:23 UTC  
> Updated_at: 2019-06-07 06:04:44 UTC  
> Url: https://github.com/boostorg/range/pull/92#issuecomment-499768099  

@Mike-Devel I was intending to get back around to these but just didn't find the time. I just started a new job too so it's unlikely I'll have time for quite a while. I'll close this one out, along with all the other ones I raised that haven't merged yet.

---

## Comment 5

> Username: Mike-Devel  
> Created_at: 2019-06-07 08:37:52 UTC  
> Url: https://github.com/boostorg/range/pull/92#issuecomment-499805107  

Very sorry to hear that. All the best for your new job.

---
