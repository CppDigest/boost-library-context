# #1264 test: add test case for issue #1262 [Merged]

> Username: barendgehrels  
> Created at: 2024-03-27 09:43:21 UTC  
> Updated at: 2024-12-14 11:13:43 UTC  
> Merged at: 2024-04-01 16:03:27 UTC  
> Closed at: 2024-04-01 16:03:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/1264  



---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2024-03-27 09:44:52 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1264#pullrequestreview-1962787815  

📁 test/algorithms/buffer/test_buffer_svg_per_turn.hpp

```diff
  16 | #define BOOST_GEOMETRY_TEST_BUFFER_SVG_PER_TURN_HPP
  17 | 
  18 |+ #if defined(TEST_WITH_SVG_PER_TURN)
```

> Username: barendgehrels  
> Created_at: 2024-03-27 09:44:52 UTC  
> Updated_at: 2024-03-27 09:45:22 UTC  
> Url: https://github.com/boostorg/geometry/pull/1264#discussion_r1540765574  

This only needs to be compiled if this define is set.  
Otherwise, `#include <boost/ptr_container/ptr_vector.hpp>` fails (if `TEST_WITH_SVG` is set) because `ptr_vector` is currently not in the CMakeLists and I don't want to add another dependency for this.  
  
It's years ago I used this svg per turn, and I think it can be removed.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2024-03-27 09:48:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1264#pullrequestreview-1962796995  

LGTM, thanks!

---
