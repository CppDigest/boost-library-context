# #758 Conform the countset, replace many #ifdefs with expectation limits [Merged]

> Username: barendgehrels  
> Created at: 2020-09-30 11:53:30 UTC  
> Updated at: 2020-10-21 08:21:23 UTC  
> Merged at: 2020-10-21 08:21:17 UTC  
> Closed at: 2020-10-21 08:21:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/758  

I was already busy with this before the rework on buffer.  
  
This is preparing a next commit and avoiding many ifdefs for situations which are OK (just for float a bit more slack, or for more precise types a different amount of output rings or small slivers.  
  
Also you can see the state - often without rescaling tests perform better than with. But I still have to verify all robustness tests too, and there is a PR in preparation to include start turns, which can be necessary in some cases (if rescaling if removed)

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2020-10-14 09:22:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/758#issuecomment-708277182  

I updated the PR (fixed conflict)

---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2020-10-15 23:48:40 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/758#pullrequestreview-509890646  

📁 test/algorithms/buffer/test_buffer.hpp

```diff
  28 |- #include "geometry_test_common.hpp"
  28 |+ #include <geometry_test_common.hpp>
  29 |+ #include <expectation_limits.hpp>
```

> Username: awulkiew  
> Created_at: 2020-10-15 23:48:40 UTC  
> Updated_at: 2020-10-21 08:19:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/758#discussion_r505926206  

Where is this file located? I can't find it.

> Username: barendgehrels  
> Created_at: 2020-10-16 16:33:09 UTC  
> Updated_at: 2020-10-21 08:19:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/758#discussion_r506590090  

Oops - it wasn't there. But now it is. Thank you.


---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2020-10-16 08:51:21 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/758#pullrequestreview-510272518  

I see some indentation issues and a file that is probably missing. Apart from those I am OK.

📁 test/geometry_test_common.hpp

```diff
 238 |+ 
 239 |+ inline void BoostGeometryWriteExpectedFailures(std::size_t for_rescaling,
 240 |+                 std::size_t for_no_rescaling_double = BG_NO_FAILURES)
```

> Username: vissarion  
> Created_at: 2020-10-16 08:47:21 UTC  
> Updated_at: 2020-10-21 08:19:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/758#discussion_r506182599  

what is the correct indentation here?

---

📁 test/geometry_test_common.hpp

```diff
 240 |+                 std::size_t for_no_rescaling_double = BG_NO_FAILURES)
 241 |+ {
 242 |+   BoostGeometryWriteExpectedFailures(for_rescaling, for_no_rescaling_double,
```

> Username: vissarion  
> Created_at: 2020-10-16 08:47:50 UTC  
> Updated_at: 2020-10-21 08:19:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/758#discussion_r506182942  

also here I see an indentation issue

> Username: barendgehrels  
> Created_at: 2020-10-16 16:46:49 UTC  
> Updated_at: 2020-10-21 08:19:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/758#discussion_r506597260  

oh yes, sorry about this.  
I'm working on another machine now with the default 2 (which is actually **much** better but not the boost convention).  
I'll fix it later. Thanks.


📁 test/algorithms/set_operations/union/union.cpp

```diff
 380 |+        settings.set_test_validity(BG_IF_RESCALED(true, false));
 381 |+        TEST_UNION_WITH(isovist, 1, 0, -1, 313.36036462);
 382 |+      }
```

> Username: vissarion  
> Created_at: 2020-10-16 08:49:40 UTC  
> Updated_at: 2020-10-21 08:19:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/758#discussion_r506184111  

indentation mistake?


📁 test/algorithms/set_operations/union/test_union.hpp

```diff
 198 |+     if (! expected_hole_count.empty())
 199 |+     {
 200 |+       BOOST_CHECK_MESSAGE(expected_hole_count.has(holes),
```

> Username: vissarion  
> Created_at: 2020-10-16 08:50:37 UTC  
> Updated_at: 2020-10-21 08:19:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/758#discussion_r506184748  

indentation mistake?

> Username: barendgehrels  
> Created_at: 2020-10-21 08:20:48 UTC  
> Updated_at: 2020-10-21 08:21:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/758#discussion_r509082164  

:heavy_check_mark:   
Indentation issues fixed.


---
