# #63 resolve some warnings [Merged]

> Username: jeking3  
> Created at: 2018-07-17 14:01:43 UTC  
> Updated at: 2018-08-29 02:18:49 UTC  
> Merged at: 2018-08-25 17:11:21 UTC  
> Closed at: 2018-08-25 17:11:21 UTC  
> Url: https://github.com/boostorg/regex/pull/63  

This addresses part of #62   
Let's see how it fares in CI.  
I was able to build the following successfully with gcc 7.3 (Ubuntu Bionic):  
  
```  
boost@7d92b0355630:/boost/libs/regex/test$ ../../../b2 -q warnings-as-errors=on cxxflags="-Wno-unused-local-typedefs" cxxflags="-Wno-unused-but-set-variable"  
```

---

## Review 1 [Commented]

> Username: jzmaddock  
> Created_at: 2018-07-19 07:11:59 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/regex/pull/63#pullrequestreview-138546132  

📁 include/boost/regex/concepts.hpp

```diff
 438 |       ignore_unused_variable_warning(diff);
 439 |       // match_results tests:
 441 |-       typedef typename match_results_type::value_type mr_value_type;
```

> Username: jzmaddock  
> Created_at: 2018-07-19 07:11:59 UTC  
> Updated_at: 2018-07-20 18:35:59 UTC  
> Url: https://github.com/boostorg/regex/pull/63#discussion_r203621940  

I appreciate that these are unused, but the whole point of these is to verify that the match_result nested typedefs exist.  Removing them simply removes the test.

> Username: jeking3  
> Created_at: 2018-07-19 10:45:16 UTC  
> Updated_at: 2018-07-20 18:35:59 UTC  
> Url: https://github.com/boostorg/regex/pull/63#discussion_r203681413  

Makes sense.  I'll undo those changes.  Does this comment apply to line 367 as well?

> Username: jzmaddock  
> Created_at: 2018-07-20 17:56:24 UTC  
> Updated_at: 2018-07-20 18:35:59 UTC  
> Url: https://github.com/boostorg/regex/pull/63#discussion_r204123437  

Yes, everything in that file.


---
