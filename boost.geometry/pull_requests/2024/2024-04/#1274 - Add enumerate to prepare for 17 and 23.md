# #1274 Add enumerate to prepare for 17 and 23 [Merged]

> Username: barendgehrels  
> Created at: 2024-04-21 07:45:29 UTC  
> Updated at: 2024-04-24 13:56:26 UTC  
> Merged at: 2024-04-24 13:56:21 UTC  
> Closed at: 2024-04-24 13:56:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/1274  

I was recently pointed at `enumerate` which has similar behavior as our `...with_index`, but better.  
So I propose to change that function with lambdas (I wrote it recently) into this view/range based approach, to be better prepared for C++17 (structured bindings) and C++23 (views::enumerate), avoid the lambda and the closures, and be more in line with the C++ direction.  
  
If you like it, I'll change the other occurences of `for_each_with_index` too and we can deprecate it or remove it (it's in namespace `detail`)

---

## Review 1 [Approved]

> Username: awulkiew  
> Created_at: 2024-04-22 22:07:05 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1274#pullrequestreview-2015878497  

That's interesting. Thanks!

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2024-04-24 07:53:33 UTC  
> Url: https://github.com/boostorg/geometry/pull/1274#issuecomment-2074309397  

Because of the approvals already, I changed the other occurences as well.  
If @vissarion is also fine it can be merged.

---

## Review 3 [Approved]

> Username: vissarion  
> Created_at: 2024-04-24 08:31:40 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1274#pullrequestreview-2019170665  

Thanks!

📁 include/boost/geometry/views/enumerate_view.hpp

```diff
  49 |+     };
  50 |+ 
  51 |+  private:
```

> Username: vissarion  
> Created_at: 2024-04-24 08:03:38 UTC  
> Updated_at: 2024-04-24 08:31:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1274#discussion_r1577461726  

Please use the indentation of "public:" below. Similarly adjust the comment below.

> Username: barendgehrels  
> Created_at: 2024-04-24 09:03:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/1274#discussion_r1577549918  

✅


---

## Review 4 [Commented]

> Username: vissarion  
> Created_at: 2024-04-24 08:34:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1274#pullrequestreview-2019246667  

📁 include/boost/geometry/algorithms/detail/overlay/check_enrich.hpp

```diff
  27 | 
  28 | #include <boost/geometry/algorithms/detail/overlay/overlay_type.hpp>
  29 |+ #include <boost/geometry/views/enumerate.hpp>
```

> Username: vissarion  
> Created_at: 2024-04-24 08:34:12 UTC  
> Updated_at: 2024-04-24 08:34:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1274#discussion_r1577507169  

this should be `#include <boost/geometry/views/enumerate_view.hpp>`

> Username: barendgehrels  
> Created_at: 2024-04-24 08:44:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/1274#discussion_r1577523285  

Oh - that slipped through my cmake. Thanks!  
I got mails about this too, so that works great.

> Username: barendgehrels  
> Created_at: 2024-04-24 09:03:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/1274#discussion_r1577550748  

✅


---

## Review 5 [Changes requested]

> Username: vissarion  
> Created_at: 2024-04-24 08:35:10 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/geometry/pull/1274#pullrequestreview-2019250895  

There is a error in a header file include that should be changed before merging.

---

## Review 6 [Commented]

> Username: barendgehrels  
> Created_at: 2024-04-24 11:22:24 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1274#pullrequestreview-2019591575  

📁 include/boost/geometry/algorithms/detail/overlay/enrich_intersection_points.hpp

```diff
 549 |+     {
 550 |+         detail::overlay::check_graph(turns, target_operation);
 551 |+     }
```

> Username: barendgehrels  
> Created_at: 2024-04-24 11:22:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1274#discussion_r1577722044  

(Slightly) related change which enforces compilation of `check_graph` (it was broken in 2 ways,  
one by my PR, the other because `for_operation` is renamed somewhen).  
  
@vissarion is this OK?

> Username: vissarion  
> Created_at: 2024-04-24 11:35:22 UTC  
> Updated_at: 2024-04-24 11:35:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/1274#discussion_r1577736368  

yes, thanks.


---

## Comment 7

> Username: barendgehrels  
> Created_at: 2024-04-24 12:54:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/1274#issuecomment-2074881624  

Github has currently performance problems.  
Waiting until it can check again.

---
