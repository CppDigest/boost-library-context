# #904 Fix compilation errors in extensions and tests. [Merged]

> Username: awulkiew  
> Created at: 2021-09-01 13:56:12 UTC  
> Updated at: 2021-09-22 10:35:20 UTC  
> Merged at: 2021-09-22 10:35:19 UTC  
> Closed at: 2021-09-22 10:35:20 UTC  
> Url: https://github.com/boostorg/geometry/pull/904  



---

## Comment 1

> Username: awulkiew  
> Created_at: 2021-09-02 13:07:04 UTC  
> Url: https://github.com/boostorg/geometry/pull/904#issuecomment-911661420  

Thanks for the review!  
I added an update/fix for endian ordering checks after moving from Boost.Endian to Boost.Predef.

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2021-09-22 08:31:28 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/904#pullrequestreview-760609516  

📁 extensions/test/algorithms/connect.cpp

```diff
  63 | 
  60 |-     BOOST_FOREACH(GeometryOut& connected, connected_vector)
  64 |+     for (GeometryOut& connected : connected_vector)
```

> Username: barendgehrels  
> Created_at: 2021-09-22 08:31:02 UTC  
> Updated_at: 2021-09-22 08:31:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/904#discussion_r713714423  

Thanks


---
