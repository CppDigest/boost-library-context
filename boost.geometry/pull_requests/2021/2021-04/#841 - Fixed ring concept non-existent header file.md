# #841 Fixed ring concept non-existent header file [Merged]

> Username: Siddharth-coder13  
> Created at: 2021-04-12 09:51:27 UTC  
> Updated at: 2021-05-27 23:59:07 UTC  
> Merged at: 2021-05-27 23:59:06 UTC  
> Closed at: 2021-05-27 23:59:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/841  

Fixed error #793

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2021-05-12 08:25:12 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/841#pullrequestreview-657602892  

📁 doc/concept/ring.qbk

```diff
  40 | * a Boost.Polygon polygon_data (requires `#include boost/geometry/geometries/adapted/boost_polygon/ring.hpp>`)
  41 |- * a std::vector (requires `#include boost/geometry/geometries/adapted/std_as_ring.hpp>`)
  42 |- * a std::deque (requires `#include boost/geometry/geometries/adapted/std_as_ring.hpp>`)
```

> Username: barendgehrels  
> Created_at: 2021-05-12 08:25:05 UTC  
> Updated_at: 2021-05-12 08:25:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/841#discussion_r630831687  

Thanks!

> Username: awulkiew  
> Created_at: 2021-05-26 16:16:33 UTC  
> Updated_at: 2021-05-26 16:16:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/841#discussion_r639886386  

An alternative would be to replace them with `boost/geometry/geometries/register/ring.hpp` but I'm fine with removing them.


---
