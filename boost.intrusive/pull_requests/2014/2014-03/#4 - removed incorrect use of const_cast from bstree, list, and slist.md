# #4 removed incorrect use of const_cast from bstree, list, and slist [Closed]

> Username: mateidavid  
> Created at: 2014-03-31 23:12:07 UTC  
> Updated at: 2014-08-04 15:36:33 UTC  
> Closed at: 2014-05-01 14:27:35 UTC  
> Url: https://github.com/boostorg/intrusive/pull/4  

When `pointer_traits<pointer>::reference` is not a real reference, one cannot use `const_cast` to convert `const_reference` into a `reference`. Instead, one should go through pointers, and use `pointer_traits<pointer>::const_cast_from`.  
  
I removed the use of operator `const_cast` from `bstree`, `list`, and `slist`. The operator is still being used inside `hashtable`.

---

## Comment 1

> Username: igaztanaga  
> Created_at: 2014-05-01 14:27:35 UTC  
> Url: https://github.com/boostorg/intrusive/pull/4#issuecomment-41914368  

Thanks. I've reviewed the patch and spotted several more const_cast removals (in unordered containers). Detected [s_]iterator_to was not tested in many containers and fixed it.  
  
Fixed in develop branch: SHA-1: 7fc779ff0d28f0fcc603805eec00ac33442a045b  
- Fixed #9948: remove use of const_cast in intrusive containers.  
  Added tests for [s_]iterator_to.

---
