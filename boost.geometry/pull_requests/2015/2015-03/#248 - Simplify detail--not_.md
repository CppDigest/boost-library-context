# #248 Simplify detail::not_ [Merged]

> Username: mkaravel  
> Created at: 2015-03-08 09:45:26 UTC  
> Updated at: 2015-03-08 14:54:55 UTC  
> Merged at: 2015-03-08 14:01:28 UTC  
> Closed at: 2015-03-08 14:01:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/248  

Modify the implementation of `detail::not_` by moving the geometry template parameters inside the class.  
This way it is simpler to use `detail::not_` as it is not required to specify the geometries.

---

## Comment 1

> Username: barendgehrels  
> Created_at: 2015-03-08 14:01:25 UTC  
> Url: https://github.com/boostorg/geometry/pull/248#issuecomment-77750321  

OK for me

---
