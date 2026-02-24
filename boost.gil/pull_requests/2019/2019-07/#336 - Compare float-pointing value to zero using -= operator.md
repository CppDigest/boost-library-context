# #336 Compare float-pointing value to zero using <= operator [Merged]

> Username: mloskot  
> Created at: 2019-07-14 15:27:34 UTC  
> Updated at: 2020-02-22 13:12:29 UTC  
> Merged at: 2019-07-16 00:33:20 UTC  
> Closed at: 2019-07-16 00:33:20 UTC  
> Url: https://github.com/boostorg/gil/pull/336  

### Description  
  
Fixes `-Wfloat-equal` warning.  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Commented]

> Username: stefanseefeld  
> Created_at: 2019-07-14 16:14:48 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/336#pullrequestreview-261589460  

📁 include/boost/gil/image_processing/numeric.hpp

```diff
  41 |-     if (x == 0)
  41 |+     // means == but <= avoids compiler warning
  42 |+     if (0 <= x && x <= 0)
```

> Username: stefanseefeld  
> Created_at: 2019-07-14 16:14:48 UTC  
> Url: https://github.com/boostorg/gil/pull/336#discussion_r303251171  

Don't we have an `is_equal()` function for floating-point numbers ? We probably should...

> Username: mloskot  
> Created_at: 2019-07-14 16:22:38 UTC  
> Url: https://github.com/boostorg/gil/pull/336#discussion_r303251430  

I am not aware of such function.  
  
There are FP comparison tools in Boost.Test, https://www.boost.org/doc/libs/1_70_0/libs/math/doc/html/utils.html, but those may impose a performance hit. So, I went for a minimal compiler-friendly way.  
  
If you or @simmplecoder prefer, feel free to add `is_equal`, then I'll refactor all FP tests from `==` to use such function.

> Username: mloskot  
> Created_at: 2019-07-15 22:52:22 UTC  
> Url: https://github.com/boostorg/gil/pull/336#discussion_r303668914  

FYI, @simmplecoder, alternative implementations in GDAL do this  
  
https://github.com/OSGeo/gdal/blob/b1c9c12ad373e40b955162b45d704070d4ebf7b0/gdal/alg/gdalwarpkernel.cpp#L3020-L3023  
  
https://github.com/OSGeo/gdal/blob/b1c9c12ad373e40b955162b45d704070d4ebf7b0/gdal/alg/gdalwarpkernel_opencl.cpp#L1145-L1150


---
