# #1140 Drop dependencies, replace boost with std, remove includes [Merged]

> Username: awulkiew  
> Created at: 2023-04-23 12:23:22 UTC  
> Updated at: 2023-06-16 14:03:54 UTC  
> Merged at: 2023-05-16 08:44:31 UTC  
> Closed at: 2023-05-16 08:44:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/1140  

Drop Boost.SmartPtr dependency  
Drop Boost.Utility (Call Traits) dependency  
Replace `boost::array` with `std::array`  
Replace `boost::minmax_element` with `std::minmax_element` Remove unneeded headers of Boost.Tuple and Boost.Algorithm Include specific headers from Boost.StringAlgo and Boost.Thread  
Use auto for iterator types

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2023-04-23 15:11:16 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1140#pullrequestreview-1396924722  

Thanks! Looks good.  
  
The only question I have: do we still separate commits between extensions and normal code, or is it equally convenient to mix it?

---

## Review 2 [Commented]

> Username: tinko92  
> Created_at: 2023-04-24 06:45:05 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/1140#pullrequestreview-1397287970  

📁 include/boost/geometry/strategies/geographic/closest_points_pt_seg.hpp

```diff
  14 | 
  15 | #include <algorithm>
```

> Username: tinko92  
> Created_at: 2023-04-24 06:45:04 UTC  
> Updated_at: 2023-04-24 06:45:05 UTC  
> Url: https://github.com/boostorg/geometry/pull/1140#discussion_r1174849425  

Is this include possibly unused too?

> Username: awulkiew  
> Created_at: 2023-04-24 07:42:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1140#discussion_r1174901132  

Yes, and several others in this file. They were probably copied from spherical version. I'll remove them.


---

## Comment 3

> Username: awulkiew  
> Created_at: 2023-04-24 07:41:13 UTC  
> Url: https://github.com/boostorg/geometry/pull/1140#issuecomment-1519548253  

@barendgehrels I didn't remember that we do this. Certainly this would make cherry-picking easier into master. I'll separate the PR into two commits.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2023-05-09 08:39:08 UTC  
> Url: https://github.com/boostorg/geometry/pull/1140#issuecomment-1539694863  

@vissarion are you ok with these changes?

---

## Review 5 [Approved]

> Username: vissarion  
> Created_at: 2023-05-09 11:50:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1140#pullrequestreview-1418492237  

Thanks, I am OK with merging.

---
