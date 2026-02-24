# #1220 Change BOOST_MATH_PROMOTE_DOUBLE_POLICY to false for non-x86 [Open]

> Username: rdoeffinger  
> Created at: 2024-11-20 16:46:59 UTC  
> Updated at: 2024-11-20 19:10:24 UTC  
> Url: https://github.com/boostorg/math/pull/1220  

Avoids a massive performance loss due to use of emulated 128 bit types. Fixes issue #1211.  
  
Note: to avoid breaking lots of tests on these platforms, this should be merged after https://github.com/boostorg/math/pull/1214

---
