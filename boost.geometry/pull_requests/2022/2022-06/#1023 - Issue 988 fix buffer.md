# #1023 Issue 988 fix buffer [Merged]

> Username: barendgehrels  
> Created at: 2022-06-22 11:47:25 UTC  
> Updated at: 2022-06-29 09:57:27 UTC  
> Merged at: 2022-06-29 09:57:24 UTC  
> Closed at: 2022-06-29 09:57:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/1023  

This replaces #993 and is a thorough revision of turn-in-ring-winding, now using the side-rounded-input to determine if the turn is on the border, or not.  
It fixes issue #988 , and more cases.  
The min-distance disappeared, but I had to replace it back (in another, simpler way) to still support rescaling.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2022-06-23 10:56:18 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1023#pullrequestreview-1016803367  

Looks good to me.

---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2022-06-23 12:42:03 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1023#pullrequestreview-1016932057  

📁 include/boost/geometry/algorithms/detail/buffer/buffered_piece_collection.hpp

```diff
 852 |             point_type const& original_point2,
 920 |-             Range const& range, bool first)
 853 |+             Range const& range, bool first, bool empty)
```

> Username: awulkiew  
> Created_at: 2022-06-23 12:42:03 UTC  
> Updated_at: 2022-06-23 12:48:40 UTC  
> Url: https://github.com/boostorg/geometry/pull/1023#discussion_r904969966  

I'd call it `is_empty` to avoid confusion with `container.empty()` or C++17 `std::empty()`. And because it's `bool`.  
  
EDIT: the same goes for `is_first` which can be confused with range iterators which are typically called `first` and `last`.

> Username: barendgehrels  
> Created_at: 2022-06-29 09:39:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/1023#discussion_r909421441  

:heavy_check_mark:


---
