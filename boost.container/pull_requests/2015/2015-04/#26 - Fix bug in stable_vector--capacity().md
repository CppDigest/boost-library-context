# #26 Fix bug in stable_vector::capacity() [Merged]

> Username: timsong-cpp  
> Created at: 2015-04-19 08:31:20 UTC  
> Updated at: 2015-04-19 09:16:16 UTC  
> Merged at: 2015-04-19 09:09:22 UTC  
> Closed at: 2015-04-19 09:09:22 UTC  
> Url: https://github.com/boostorg/container/pull/26  

See http://stackoverflow.com/q/29727368/2756719; the bit-twiddling version is not equivalent to the original `return (index_size ? (index_size - ExtraPointers + extra_capacity) : index_size);`; it ends up subtracting, rather than adding, extra_capacity.

---

## Comment 1

> Username: timsong-cpp  
> Created_at: 2015-04-19 08:35:41 UTC  
> Url: https://github.com/boostorg/container/pull/26#issuecomment-94251892  

Bah, didn't notice that I wasn't on the develop branch. But since it's a one character change....

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2015-04-19 09:11:44 UTC  
> Updated_at: 2015-04-19 09:16:16 UTC  
> Url: https://github.com/boostorg/container/pull/26#issuecomment-94254656  

Many thanks for the patch, it's surprising and embarrasing this has not fixed being before.

---
