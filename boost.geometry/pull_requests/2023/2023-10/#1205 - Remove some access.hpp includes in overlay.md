# #1205 Remove some access.hpp includes in overlay [Merged]

> Username: tinko92  
> Created at: 2023-10-02 06:17:26 UTC  
> Updated at: 2023-12-04 10:46:39 UTC  
> Merged at: 2023-12-01 13:03:42 UTC  
> Closed at: 2023-12-01 13:03:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/1205  

This removes the include of access.hpp from some files in the overlay algorithm that don't use it (AFAICS they don't use get, set or the {max,min}_corner constants) and moves the box_box_intersection implementation out of the overlay algorithm where it is not used directly.  
  
(This is a by-product of my larger attempt to locate and separate direct computations involving coordinates from the overlay-code, which I think is a prerequisite for implementing and testing numerically robust strategies. Because I don't know how long that might take, whether it will succeed and how much the develop-branch might change in the mean time, I thought it made sense to push this self-contained change early.)

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2023-10-04 11:26:07 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1205#pullrequestreview-1657294691  

Thanks!

📁 include/boost/geometry/algorithms/detail/overlay/get_turns.hpp

```diff
 742 |         else return 0;
 744 |-     }
 743 |+     }*/
```

> Username: barendgehrels  
> Created_at: 2023-10-04 11:23:29 UTC  
> Updated_at: 2023-10-04 11:26:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1205#discussion_r1345634103  

You can remove this one. I don't remember exactly but I probably should have cleaned it up earlier.

> Username: tinko92  
> Created_at: 2023-10-04 12:26:48 UTC  
> Updated_at: 2023-10-04 12:26:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1205#discussion_r1345713924  

I removed the get_side function that was commented out and the commented out that was calling it. If the latter was meant to be kept, I can change the commit.

> Username: barendgehrels  
> Created_at: 2023-10-04 20:05:53 UTC  
> Updated_at: 2023-10-04 20:05:54 UTC  
> Url: https://github.com/boostorg/geometry/pull/1205#discussion_r1346400096  

This is completely fine, thanks!


📁 include/boost/geometry/algorithms/detail/intersection/box_box.hpp

```diff
  18 | #include <boost/geometry/algorithms/detail/intersection/interface.hpp>
  19 |- #include <boost/geometry/algorithms/detail/overlay/intersection_box_box.hpp>
  19 |+ #include <boost/geometry/algorithms/detail/intersection/box_box_implementation.hpp>
```

> Username: barendgehrels  
> Created_at: 2023-10-04 11:25:06 UTC  
> Updated_at: 2023-10-04 11:26:07 UTC  
> Url: https://github.com/boostorg/geometry/pull/1205#discussion_r1345635975  

Why is this? I'm not against, but we don't have this convention.

> Username: tinko92  
> Created_at: 2023-10-04 12:44:34 UTC  
> Updated_at: 2023-10-04 12:44:35 UTC  
> Url: https://github.com/boostorg/geometry/pull/1205#discussion_r1345737291  

The reason for moving the code of intersection_box_box.hpp out of overlay was that it was not used there directly.  detail/intersection-directory seemed like a better fit.  
The reason for not moving the code into in ./algorithms/detail/intersection/box_box.hpp was that I could not include that header into ./index/detail/algorithms/intersection_content.hpp, where the code is also used, without test failures due other dependencies between relate and rtree. I did not try to resolve those because it seemed sensible that the content of intersection_box_box.hpp, which looked useful and in itself not dependent on anything non-core, would remain in its own header file. It also did not look like its logic could ever be CS-dependent or warrant an alternative implementation, so I did not move it into a strategy.  
The reason for appending _implementation was that the name box_box.hpp was already taken in that directory by the header with the dispatch code.

> Username: barendgehrels  
> Created_at: 2023-10-04 20:03:58 UTC  
> Updated_at: 2023-10-04 20:03:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/1205#discussion_r1346398142  

All right, clear, thanks!


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2023-12-01 13:03:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1205#pullrequestreview-1759772565  

Thanks LGTM.

---
