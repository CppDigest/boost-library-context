# #456 Fix transform_if() test [Merged]

> Username: kylelutz  
> Created at: 2015-05-17 18:06:37 UTC  
> Updated at: 2015-05-18 03:15:24 UTC  
> Merged at: 2015-05-18 03:15:22 UTC  
> Closed at: 2015-05-18 03:15:22 UTC  
> Url: https://github.com/boostorg/compute/pull/456  

This fixes the transform_if() test to copy to a separate output  
vector as in-place operation is not supported.

---
