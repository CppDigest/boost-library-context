# #190 Fix const on return type not applied to pointee type of typedef [Merged]

> Username: mloskot  
> Created at: 2018-12-13 23:18:18 UTC  
> Updated at: 2019-03-29 20:29:13 UTC  
> Merged at: 2018-12-14 07:12:46 UTC  
> Closed at: 2018-12-14 07:12:46 UTC  
> Url: https://github.com/boostorg/gil/pull/190  

Replace `png_structp` and `png_infop` aliases with regular pointers to to prevent type qualifiers ignored on function return type.  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed  
  
-----  
  
By the way, the alias is defined as `typedef png_struct FAR * png_structp;` and I think we are safe to ignore the `FAR` qualifier :)

---

## Review 1 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-12-13 23:51:46 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/190#pullrequestreview-184921136  

Wonderful !

---
