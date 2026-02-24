# #820 [svg] changes newly added scale function in a constructor parameter [Merged]

> Username: barendgehrels  
> Created at: 2021-02-24 12:30:53 UTC  
> Updated at: 2021-03-03 09:54:40 UTC  
> Merged at: 2021-03-03 09:54:34 UTC  
> Closed at: 2021-03-03 09:54:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/820  

This addresses the comments of @awulkiew   
  
Note that it is not 100% backward compatible: all generated SVG's will now have a default margin of 5%. I'm not 100% sure about that. Maybe we should revert that (having a 100% scale initially). There might be customers having these as test results.

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2021-02-24 12:33:14 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/820#pullrequestreview-597427562  

📁 include/boost/geometry/io/svg/svg_mapper.hpp

```diff
 367 |+     {
 368 |+         assign_inverse(m_bounding_box);
 369 |+         write_header(width_height);
```

> Username: barendgehrels  
> Created_at: 2021-02-24 12:33:14 UTC  
> Updated_at: 2021-03-03 09:42:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/820#discussion_r581920714  

The header is now written immediately. Therefore uses can stream, for example arrow-definitions, right after the constructor (in previous version it needed to be done after `scale`).  
  
Also the scale is here now and therefore I had to add a second version of the constructor (because having it after the width_height is inconvenient)

> Username: awulkiew  
> Created_at: 2021-02-24 13:24:40 UTC  
> Updated_at: 2021-03-03 09:42:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/820#discussion_r581955149  

Yes, the default `scale = 1` seems to be more intuitive.

> Username: barendgehrels  
> Created_at: 2021-02-25 20:24:20 UTC  
> Updated_at: 2021-03-03 09:42:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/820#discussion_r583147654  

I'll change it

> Username: barendgehrels  
> Created_at: 2021-03-03 09:48:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/820#discussion_r586268998  

:heavy_check_mark: changed to `1.0`


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2021-03-01 09:46:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/820#pullrequestreview-600550350  

Thanks, I am OK.

---
