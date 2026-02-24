# #1216 fix: define BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS for emscripten [Merged]

> Username: NickelWenzel  
> Created at: 2024-10-23 07:22:59 UTC  
> Updated at: 2024-10-28 08:15:45 UTC  
> Merged at: 2024-10-24 13:01:18 UTC  
> Closed at: 2024-10-24 13:01:18 UTC  
> Url: https://github.com/boostorg/math/pull/1216  

Extracted check for `emscripten` and define `BOOST_MATH_NO_LONG_DOUBLE_MATH_FUNCTIONS` in case it wasn't previously defined.  
  
This should fix #1215.

---

## Comment 1

> Username: NickelWenzel  
> Created_at: 2024-10-24 08:53:32 UTC  
> Url: https://github.com/boostorg/math/pull/1216#issuecomment-2434681907  

@mborland could you maybe trigger the drone pipeline again, the clone step did not succeed for some configuration: https://drone.cpp.al/boostorg/math/2113/286/1

---

## Comment 2

> Username: mborland  
> Created_at: 2024-10-24 13:01:08 UTC  
> Updated_at: 2024-10-24 13:02:05 UTC  
> Url: https://github.com/boostorg/math/pull/1216#issuecomment-2435234950  

> @mborland could you maybe trigger the drone pipeline again, the clone step did not succeed for some configuration: https://drone.cpp.al/boostorg/math/2113/286/1  
  
That happens periodically on that specific platform. I think we're safe to merge.

---
