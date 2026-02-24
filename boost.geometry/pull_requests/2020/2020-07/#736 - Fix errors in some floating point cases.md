# #736 Fix errors in some floating point cases [Merged]

> Username: barendgehrels  
> Created at: 2020-07-15 11:43:08 UTC  
> Updated at: 2020-07-22 09:57:40 UTC  
> Merged at: 2020-07-22 09:57:36 UTC  
> Closed at: 2020-07-22 09:57:36 UTC  
> Url: https://github.com/boostorg/geometry/pull/736  

* in intersection a check is made for overlap (solving some errors mainly in small scale AND nearly collinear situations)  
* in touch a special case is added where intersection reports touching segments, but they are just not touching  
* in touch some sides were reported as 0 while, in FP, they are distinguishable and that can be used to fix some errors

---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-18 02:00:21 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/736#pullrequestreview-451022338  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 140 |+         typedef detail::distance_measure<coordinate_type> dm_type;
 141 |+ 
 142 |+         dm_type const dm = get_distance_measure(range_p.at(range_index), range_p.at(range_index + 1), range_q.at(point_index));
```

> Username: awulkiew  
> Created_at: 2020-07-18 02:00:20 UTC  
> Updated_at: 2020-07-22 09:56:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/736#discussion_r456735367  

What are these ranges? I don't remember. Shouldn't the type of the index rather be some kind of unsigned integral type like `size_t` or ideally defined by the `UniqueSubRange` type as e.g. `size_type`?

> Username: barendgehrels  
> Created_at: 2020-07-18 14:33:31 UTC  
> Updated_at: 2020-07-22 09:56:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/736#discussion_r456795656  

We pass small ranges to be able to know the next points (point after two intersecting segments).  
I will look what we normally do, assuming indeed we can pass size_t

> Username: awulkiew  
> Created_at: 2020-07-18 16:20:07 UTC  
> Updated_at: 2020-07-22 09:56:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/736#discussion_r456804764  

So the size of the range is 1, 2 or 3. Ok, bigger type is not needed then.

> Username: barendgehrels  
> Created_at: 2020-07-22 09:43:02 UTC  
> Updated_at: 2020-07-22 09:56:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/736#discussion_r458669188  

Indeed, OK, thanks


---

## Review 2 [Commented]

> Username: awulkiew  
> Created_at: 2020-07-18 02:01:09 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/736#pullrequestreview-451022391  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 460 |+                     typename UniqueSubRange1::point_type,
 461 |+                     typename UniqueSubRange2::point_type
 462 |+                     >::type coordinate_type;
```

> Username: awulkiew  
> Created_at: 2020-07-18 02:01:08 UTC  
> Updated_at: 2020-07-22 09:56:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/736#discussion_r456735448  

Too much indentation. ;)

> Username: barendgehrels  
> Created_at: 2020-07-18 14:33:43 UTC  
> Updated_at: 2020-07-22 09:56:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/736#discussion_r456795686  

will fix, thanks & thanks for your review

> Username: barendgehrels  
> Created_at: 2020-07-22 09:57:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/736#discussion_r458677379  

all fixed, amended and pushed


---

## Review 3 [Approved]

> Username: awulkiew  
> Created_at: 2020-07-18 02:02:40 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/736#pullrequestreview-451022493  

Looks good, thanks!

---

## Review 4 [Approved]

> Username: vissarion  
> Created_at: 2020-07-21 07:30:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/736#pullrequestreview-452190673  

I am OK with merging, thanks!

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 120 |+     >
 121 |+     static inline int side_with_distance_measure(UniqueSubRange1 const& range_p,
 122 |+             UniqueSubRange2 const& range_q,
```

> Username: vissarion  
> Created_at: 2020-07-21 07:23:16 UTC  
> Updated_at: 2020-07-22 09:56:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/736#discussion_r457889719  

is indentation right here?

---

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 136 |+                     typename UniqueSubRange1::point_type,
 137 |+                     typename UniqueSubRange2::point_type
 138 |+                     >::type coordinate_type;
```

> Username: vissarion  
> Created_at: 2020-07-21 07:24:28 UTC  
> Updated_at: 2020-07-22 09:56:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/736#discussion_r457890303  

Too much indentation also here

> Username: barendgehrels  
> Created_at: 2020-07-22 09:54:34 UTC  
> Updated_at: 2020-07-22 09:56:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/736#discussion_r458675785  

Thanks, indeed. Fixed now, all in this file (there were some other ones too somehow)

---

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 431 |+     >
 432 |+     static inline bool handle_imperfect_touch(UniqueSubRange1 const& range_p,
 433 |+             UniqueSubRange2 const& range_q, TurnInfo& ti)
```

> Username: vissarion  
> Created_at: 2020-07-21 07:25:32 UTC  
> Updated_at: 2020-07-22 09:56:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/736#discussion_r457890943  

indentation issue?

> Username: barendgehrels  
> Created_at: 2020-07-22 09:52:06 UTC  
> Updated_at: 2020-07-22 09:56:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/736#discussion_r458674377  

Well, this is how we (at least I) did it from the beginning - an indentation of 8. However, currently IDE's usually automatically align it under the next bracket and some features (`clang_format`) or people also do this. So now we've a mixed approach.  
  
IMO both are not perfect... For the first method it is currently unusual, I think. For the second method, with long names and modifiers, it goes really to the right and needs more lines.  
  
I keep this for now, if you've strong opinions, just comment.


---
