# #627 Implement relops for arguments always resulting in false. [Merged]

> Username: awulkiew  
> Created at: 2019-10-16 17:56:42 UTC  
> Updated at: 2019-10-19 16:02:21 UTC  
> Merged at: 2019-10-19 16:02:21 UTC  
> Closed at: 2019-10-19 16:02:21 UTC  
> Url: https://github.com/boostorg/geometry/pull/627  

The purpose is to simplify writing generic code with relational operations. Consider e.g.:  
  
    bg::equals(linestring, polygon);  
    bg::touches(point, linestring);  
    bg::crosses(point, linestring);  
    bg::overlaps(point, linestring);  
  
before: compilation error,  
this PR: compiles and always returns `false`.

---

## Review 1 [Approved]

> Username: vissarion  
> Created_at: 2019-10-17 13:15:00 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/627#pullrequestreview-303245001  

That's useful, thanks!

---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2019-10-17 13:16:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/627#pullrequestreview-303245865  

📁 include/boost/geometry/algorithms/detail/equals/implementation.hpp

```diff
 279 |+ {
 280 |+     template <typename Geometry1, typename Geometry2, typename Strategy>
 281 |+     static inline bool apply(Geometry1 const& , Geometry2 const& , Strategy const& )
```

> Username: mloskot  
> Created_at: 2019-10-17 13:16:15 UTC  
> Url: https://github.com/boostorg/geometry/pull/627#discussion_r335996779  

Nitpicking: `& )` -> `&)` :-)

> Username: awulkiew  
> Created_at: 2019-10-18 16:05:40 UTC  
> Updated_at: 2019-10-18 16:05:41 UTC  
> Url: https://github.com/boostorg/geometry/pull/627#discussion_r336565343  

@mloskot Well idk ;) do we have gudelines for that? AFAIU we put space after `&` and before `, ` so I assumed that the missing name is represented by space. Or am I mistaken and all of the spaces should be removed?

> Username: barendgehrels  
> Created_at: 2019-10-18 16:46:03 UTC  
> Updated_at: 2019-10-18 16:46:09 UTC  
> Url: https://github.com/boostorg/geometry/pull/627#discussion_r336580691  

AFAIK we don't have a written guideline for that.  
I find this readable, but I'm also fine with &)

> Username: mloskot  
> Created_at: 2019-10-18 16:49:50 UTC  
> Url: https://github.com/boostorg/geometry/pull/627#discussion_r336582137  

Having nothing constructive to say, I went nit-picking and bike-shedding. Certainly, ignoreable

> Username: barendgehrels  
> Created_at: 2019-10-18 22:06:24 UTC  
> Url: https://github.com/boostorg/geometry/pull/627#discussion_r336692120  

Certainly nice to hear from you Mateusz!

> Username: mloskot  
> Created_at: 2019-10-19 09:08:49 UTC  
> Url: https://github.com/boostorg/geometry/pull/627#discussion_r336729041  

Barend, I'm happy to be revolving around still

> Username: awulkiew  
> Created_at: 2019-10-19 16:01:31 UTC  
> Updated_at: 2019-10-19 16:01:32 UTC  
> Url: https://github.com/boostorg/geometry/pull/627#discussion_r336742993  

Ok, I saw this in Geometry code but it is possible that it was me who put it in the first place. I don't have an opinion regarding it, but if it's ignorable I'll leave it as it is. ;)


---

## Comment 3

> Username: barendgehrels  
> Created_at: 2019-10-17 17:33:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/627#issuecomment-543281707  

So this is only for geometry-type-combinations which can, by definition, never return `true`?

---

## Comment 4

> Username: awulkiew  
> Created_at: 2019-10-18 16:01:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/627#issuecomment-543810769  

@barendgehrels Yes, other cases are already handled. There is no combination which always results in `true`.

---

## Comment 5

> Username: barendgehrels  
> Created_at: 2019-10-18 16:44:26 UTC  
> Url: https://github.com/boostorg/geometry/pull/627#issuecomment-543830353  

>   
>   
> @barendgehrels Yes, other cases are already handled. There is no combination which always results in `true`.  
  
Great, thanks

---

## Comment 6

> Username: awulkiew  
> Created_at: 2019-10-19 16:01:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/627#issuecomment-544165035  

Thanks for reviews.

---
