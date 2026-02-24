# #1134 - hausdorff and frechet do not always support custom geometries [Open]

> Username: barendgehrels  
> Created at: 2023-04-19 16:30:03 UTC  
> Updated at: 2023-04-19 16:31:15 UTC  
> Url: https://github.com/boostorg/geometry/issues/1134  

As detected in the new unit test `custom_ring.cpp`  
For linestrings, it is supported.  
But for rings, (multi)polygons, multi-linestrings, it is not
