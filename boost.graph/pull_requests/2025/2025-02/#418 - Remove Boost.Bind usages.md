# #418 Remove Boost.Bind usages. [Merged]

> Username: gogagum  
> Created at: 2025-02-11 17:44:45 UTC  
> Updated at: 2025-02-18 07:50:58 UTC  
> Merged at: 2025-02-18 07:50:34 UTC  
> Closed at: 2025-02-18 07:50:34 UTC  
> Url: https://github.com/boostorg/graph/pull/418  

This PR replaces `boost::bind` usages with lambdas, so **Boost.Bind** can be removed from dependances.

---

## Comment 1

> Username: gogagum  
> Created_at: 2025-02-11 19:00:31 UTC  
> Url: https://github.com/boostorg/graph/pull/418#issuecomment-2651799208  

I will make an attempt of doing some workaronds for Clang.

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2025-02-11 22:25:23 UTC  
> Url: https://github.com/boostorg/graph/pull/418#issuecomment-2652201243  

Thanks for your effort to improve Boost.Graph, it's really appreciated!   
However, I strongly recommend you watch this video about `bind` and then consider what change you want to make: https://youtu.be/zt7ThwVfap0?si=47AbaJkWFh-VrnBc

---

## Comment 3

> Username: gogagum  
> Created_at: 2025-02-11 22:56:21 UTC  
> Url: https://github.com/boostorg/graph/pull/418#issuecomment-2652246757  

Well, the speaker recommends to avoid `std::bind`. Do you mean this? I just tried to replace `boost::bind` with `std::bind` where possible to make minimal difference. It is not a problem to replace all `std::bind` usages with lambdas.

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2025-02-12 05:25:23 UTC  
> Url: https://github.com/boostorg/graph/pull/418#issuecomment-2652697071  

> Well, the speaker recommends to avoid `std::bind`. Do you mean this? I just tried to replace `boost::bind` with `std::bind` where possible to make minimal difference. It is not a problem to replace all `std::bind` usages with lambdas.  
  
Yeah, I think replacing one `bind` with another is not really worth doing when it could be replaced with something even better.

---

## Review 5 [Changes requested]

> Username: jeremy-murphy  
> Created_at: 2025-02-16 23:39:31 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/graph/pull/418#pullrequestreview-2619752550  

📁 include/boost/graph/king_ordering.hpp

```diff
  15 | #include <vector>
  16 | #include <algorithm>
  17 |+ #include <functional>
```

> Username: jeremy-murphy  
> Created_at: 2025-02-16 23:34:44 UTC  
> Updated_at: 2025-02-16 23:39:31 UTC  
> Url: https://github.com/boostorg/graph/pull/418#discussion_r1957443049  

Still needed?


📁 include/boost/graph/transitive_closure.hpp

```diff
 138 |+             {
 139 |+                 return std::less< cg_vertex >()(
 140 |+                     topo_number[cg_0], topo_number[cg_1]);
```

> Username: jeremy-murphy  
> Created_at: 2025-02-16 23:38:57 UTC  
> Updated_at: 2025-02-16 23:39:32 UTC  
> Url: https://github.com/boostorg/graph/pull/418#discussion_r1957443884  

Can we just use `a < b` now that it's in a lambda?

> Username: gogagum  
> Created_at: 2025-02-17 07:55:42 UTC  
> Url: https://github.com/boostorg/graph/pull/418#discussion_r1957765421  

I did not catch that `cg_vertex` if just a `size_t`, so my idea was to suggest the closest variant without analyzing what `cg_vertex` is. I would agree that it can be simplified, but there is a good thing about `std::less`: it checks the type of its arguments. So if the type for topo number is changed, there will be an compile error.

> Username: jeremy-murphy  
> Created_at: 2025-02-18 07:47:33 UTC  
> Url: https://github.com/boostorg/graph/pull/418#discussion_r1959221252  

OK, cool, I'm happy to leave it as is.


---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2025-02-16 23:44:15 UTC  
> Url: https://github.com/boostorg/graph/pull/418#issuecomment-2661681330  

I requested changes, but really they're just questions/suggestions. This code is good to go if there is a reason for everything.

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2025-02-18 07:50:57 UTC  
> Url: https://github.com/boostorg/graph/pull/418#issuecomment-2664852492  

Great, thanks for this improvement!

---
