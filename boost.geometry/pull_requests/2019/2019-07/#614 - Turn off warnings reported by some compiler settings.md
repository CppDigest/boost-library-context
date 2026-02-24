# #614 Turn off warnings reported by some compiler settings [Merged]

> Username: barendgehrels  
> Created at: 2019-07-20 14:17:42 UTC  
> Updated at: 2019-08-07 15:32:39 UTC  
> Merged at: 2019-07-22 16:47:50 UTC  
> Closed at: 2019-07-22 16:47:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/614  



---

## Review 1 [Commented]

> Username: awulkiew  
> Created_at: 2019-07-20 14:31:32 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/614#pullrequestreview-264499409  

📁 include/boost/geometry/strategies/cartesian/intersection.hpp

```diff
 235 |-             promoted_type const dx_promoted = boost::numeric_cast<promoted_type>(dx);
 236 |-             promoted_type const dy_promoted = boost::numeric_cast<promoted_type>(dy);
 229 |+             typedef typename promote_integral<CoordinateType>::type integral_type;
```

> Username: awulkiew  
> Created_at: 2019-07-20 14:31:32 UTC  
> Updated_at: 2019-07-20 14:55:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/614#discussion_r305582315  

This type may not be integral type. The purpose of this util is to promote only integral types to bigger integral types and leave other types as they were.  
  
See: https://github.com/boostorg/geometry/blob/develop/include/boost/geometry/util/promote_integral.hpp#L312

> Username: barendgehrels  
> Created_at: 2019-07-20 14:34:05 UTC  
> Updated_at: 2019-07-20 14:55:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/614#discussion_r305582402  

Thanks I'll rename it. The point was that promoted_type shadows the outer type with the same name.  
I will call it `calc_type` because this is the type calculations are done with

> Username: barendgehrels  
> Created_at: 2019-07-20 14:57:03 UTC  
> Url: https://github.com/boostorg/geometry/pull/614#discussion_r305583007  

Done


---

## Comment 2

> Username: barendgehrels  
> Created_at: 2019-07-22 16:49:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/614#issuecomment-513863795  

@awulkiew  can we still merge this one to 1.71?

---

## Comment 3

> Username: awulkiew  
> Created_at: 2019-07-22 23:00:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/614#issuecomment-513986860  

@barendgehrels This is not a complex change so I think the managers will allow it.

---

## Comment 4

> Username: awulkiew  
> Created_at: 2019-07-29 11:21:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/614#issuecomment-515951839  

@barendgehrels master branch is open for bugfixes. Are you going to cherry pick this yourself and ask the managers for the permission to merge or would you like me to do it?

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2019-07-29 19:46:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/614#issuecomment-516135617  

Hi @awulkiew , thank you. I wanted to do it this weekend but I was quite busy preparing a new PR w.r.t. rescaling (to be expected next weekend).  
If you could do it, that would be appreciated.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2019-08-07 12:23:34 UTC  
> Url: https://github.com/boostorg/geometry/pull/614#issuecomment-519073048  

@barendgehrels It's merged. Would you like to add some release notes? I plan to go through pull requests and issues and add some of them.

---

## Comment 7

> Username: barendgehrels  
> Created_at: 2019-08-07 15:32:39 UTC  
> Url: https://github.com/boostorg/geometry/pull/614#issuecomment-519149936  

@awulkiew  Thanks for merging! And for the release notes, no I currently I don't have anything to add there.

---
