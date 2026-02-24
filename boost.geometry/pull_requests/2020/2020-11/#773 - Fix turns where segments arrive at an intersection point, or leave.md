# #773 Fix turns where segments arrive at an intersection point, or leave [Merged]

> Username: barendgehrels  
> Created at: 2020-11-12 11:14:30 UTC  
> Updated at: 2020-11-25 14:38:27 UTC  
> Merged at: 2020-11-25 09:24:26 UTC  
> Closed at: 2020-11-25 09:24:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/773  

This happens in rare cases. When they arrive there (or leave), their fraction should be 1 (or 0). The fraction is calculated and is sometimes nearly 1 (or ), but not considered so.  
This can be fixed without any threshold because the information on arrival is already there.  
  
It fixes ~10% of the remaining errors in the `recursive_polygons_buffer` test.  
  
Several cases (added last PR) are fixed now. And I added 3 new cases (not yet fixed by this)

---

## Review 1 [Commented]

> Username: barendgehrels  
> Created_at: 2020-11-12 11:18:06 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/773#pullrequestreview-528987699  

📁 test/algorithms/set_operations/difference/difference.cpp

```diff
 626 |     // The difference algorithm can generate (additional) slivers
 627 |-     BoostGeometryWriteExpectedFailures(10, 11, 24, 14);
 627 |+     BoostGeometryWriteExpectedFailures(10, 11, 24, 15);
```

> Username: barendgehrels  
> Created_at: 2020-11-12 11:18:06 UTC  
> Updated_at: 2020-11-25 09:22:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/773#discussion_r522030011  

_(this was already)_

> Username: vissarion  
> Created_at: 2020-11-13 14:06:18 UTC  
> Updated_at: 2020-11-25 09:22:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/773#discussion_r522970534  

This means that there is no extra error here?

> Username: barendgehrels  
> Created_at: 2020-11-13 18:12:23 UTC  
> Updated_at: 2020-11-25 09:22:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/773#discussion_r523135334  

Nope, these statements are mainly for myself, during transition of removing rescaling. This is one for `long double`. I probably didn't update it correctly last time. It's not updated automatically.  
For this PR, I commented the code (updating the fraction) and it was still there - so it's not this PR for sure.  
  
Main purpose is to see if we get there. For set operations, the version without rescaling is (nearly) on par, or better. For buffer it's still not good enough so I'm concentrating on those cases.


---

## Review 2 [Approved]

> Username: vissarion  
> Created_at: 2020-11-13 14:24:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/773#pullrequestreview-530087866  

It looks OK to me.

---

## Comment 3

> Username: awulkiew  
> Created_at: 2020-11-23 19:14:06 UTC  
> Url: https://github.com/boostorg/geometry/pull/773#issuecomment-732368269  

@barendgehrels Do you want it to be released with 1.75?

---

## Comment 4

> Username: barendgehrels  
> Created_at: 2020-11-24 22:31:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/773#issuecomment-733271180  

>   
>   
> @barendgehrels Do you want it to be released with 1.75?  
  
Hi @awulkiew , no, it's not necessary - only for without rescaling, but that's currently not the default.

---

## Review 5 [Commented]

> Username: barendgehrels  
> Created_at: 2020-11-25 09:16:36 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/773#pullrequestreview-538292328  

📁 include/boost/geometry/algorithms/detail/overlay/get_turn_info.hpp

```diff
 187 |+         ti.method = method;
 188 |+ 
 189 |+         // Intersection points are calculated on both segments, either one can be taken.
```

> Username: barendgehrels  
> Created_at: 2020-11-25 09:16:35 UTC  
> Updated_at: 2020-11-25 09:22:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/773#discussion_r530216518  

This comment was wrong, I'll fix it and merge. Thanks for your reviews

> Username: barendgehrels  
> Created_at: 2020-11-25 09:23:19 UTC  
> Updated_at: 2020-11-25 09:23:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/773#discussion_r530221269  

Done :heavy_check_mark:


---

## Comment 6

> Username: awulkiew  
> Created_at: 2020-11-25 14:08:53 UTC  
> Url: https://github.com/boostorg/geometry/pull/773#issuecomment-733728583  

@barendgehrels since you merged it now I'll push it together with other fixes to master for 1.75.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2020-11-25 14:38:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/773#issuecomment-733745880  

>   
>   
> @barendgehrels since you merged it now I'll push it together with other fixes to master for 1.75.  
  
@awulkiew , fine, thank you!

---
