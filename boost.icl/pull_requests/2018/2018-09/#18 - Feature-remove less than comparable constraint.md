# #18 Feature/remove less than comparable constraint [Closed]

> Username: brandon-kohn  
> Created at: 2018-09-06 14:57:09 UTC  
> Updated at: 2021-02-28 20:11:16 UTC  
> Closed at: 2021-02-28 20:09:48 UTC  
> Url: https://github.com/boostorg/icl/pull/18  

Commented out LessThanComparable constraints. This allows specializations for types which do not implement operator < to work.

---

## Comment 1

> Username: jofaber  
> Created_at: 2021-02-14 18:27:53 UTC  
> Url: https://github.com/boostorg/icl/pull/18#issuecomment-778819475  

With apologies for the very late reaction... In my view it does not make sense to use intervals in the context of the icl library with value types that are lacking a strict weak ordering. I think the requirements need to remain the way they are.

---

## Comment 2

> Username: jofaber  
> Created_at: 2021-02-28 20:11:16 UTC  
> Url: https://github.com/boostorg/icl/pull/18#issuecomment-787514583  

I am closing the PR, see my last comment.

---
