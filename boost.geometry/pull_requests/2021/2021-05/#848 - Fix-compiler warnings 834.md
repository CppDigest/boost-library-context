# #848 Fix/compiler warnings 834 [Merged]

> Username: barendgehrels  
> Created at: 2021-05-12 09:42:48 UTC  
> Updated at: 2021-05-19 07:17:06 UTC  
> Merged at: 2021-05-19 07:16:57 UTC  
> Closed at: 2021-05-19 07:16:57 UTC  
> Url: https://github.com/boostorg/geometry/pull/848  

This fixes #834 and two additional new compiler warnings

---

## Review 1 [Commented]

> Username: vissarion  
> Created_at: 2021-05-12 13:56:13 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/geometry/pull/848#pullrequestreview-657918918  

Looks OK, but tests are failing on `clang-9`.

---

## Comment 2

> Username: barendgehrels  
> Created_at: 2021-05-13 08:50:12 UTC  
> Url: https://github.com/boostorg/geometry/pull/848#issuecomment-840418080  

>   
>   
> Looks OK, but tests are failing on `clang-9`.  
  
Must be a false negative  
```  
 In file included from /usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/mutex:39:  
/usr/bin/../lib/gcc/x86_64-linux-gnu/11/../../../../include/c++/11/chrono:1345:24: error: unknown type name 'strong_ordering'  
      friend constexpr strong_ordering  
```

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2021-05-13 08:51:55 UTC  
> Url: https://github.com/boostorg/geometry/pull/848#issuecomment-840418938  

I'm running them again

---

## Comment 4

> Username: awulkiew  
> Created_at: 2021-05-13 13:22:47 UTC  
> Url: https://github.com/boostorg/geometry/pull/848#issuecomment-840558271  

Yes, it seems that clang-9 problem is not in Geometry. I think it may be ignored.

---
