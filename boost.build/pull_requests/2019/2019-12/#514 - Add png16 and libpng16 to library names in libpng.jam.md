# #514 Add png16 and libpng16 to library names in libpng.jam [Merged]

> Username: mloskot  
> Created at: 2019-12-16 21:44:26 UTC  
> Updated at: 2021-10-02 21:13:39 UTC  
> Merged at: 2019-12-18 22:02:54 UTC  
> Closed at: 2019-12-18 22:02:54 UTC  
> Url: https://github.com/boostorg/build/pull/514  

On Windows, this allows to successfully find the library installed using vcpkg which deploys the library named as `libpng16.lib` and not `png16.lib` or `png.lib`.

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2019-12-18 13:11:05 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/build/pull/514#pullrequestreview-333956657  

📁 src/tools/libpng.jam

```diff
  26 | header = png.h ;
  27 |- names = png ;
  27 |+ names = libpng libpng16 png png16 ;
```

> Username: grafikrobot  
> Created_at: 2019-12-18 13:10:57 UTC  
> Updated_at: 2019-12-18 16:20:27 UTC  
> Url: https://github.com/boostorg/build/pull/514#discussion_r359333650  

Could you add a comment as to why we need those multiple names.

> Username: mloskot  
> Created_at: 2019-12-18 16:20:48 UTC  
> Updated_at: 2019-12-18 16:20:49 UTC  
> Url: https://github.com/boostorg/build/pull/514#discussion_r359438585  

Good point. Comment added.


---
