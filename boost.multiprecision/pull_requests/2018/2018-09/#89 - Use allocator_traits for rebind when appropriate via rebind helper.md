# #89 Use allocator_traits for rebind when appropriate via rebind helper [Merged]

> Username: glenfe  
> Created at: 2018-09-26 06:13:21 UTC  
> Updated at: 2018-09-27 16:53:06 UTC  
> Merged at: 2018-09-27 16:53:06 UTC  
> Closed at: 2018-09-27 16:53:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/89  



---

## Comment 1

> Username: glenfe  
> Created_at: 2018-09-26 23:23:46 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/89#issuecomment-424902607  

The single failure in Travis is not because of this change.

---

## Comment 2

> Username: glenfe  
> Created_at: 2018-09-27 13:15:29 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/89#issuecomment-425086702  

No more single failure. Everything passes after your "Fix up assignment from string_view for mpf_float" commit.

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2018-09-27 16:53:01 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/89#issuecomment-425165234  

Good catch - thanks for this!

---
