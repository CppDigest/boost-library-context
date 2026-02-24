# #44 - Add .x, .y, .z accessors to vector types [Open]

> Username: kylelutz  
> Created at: 2014-01-07 03:37:12 UTC  
> Updated at: 2017-03-21 04:14:57 UTC  
> Url: https://github.com/boostorg/compute/issues/44  

Make is possible to access components of vector types (e.g. `float4_`, `int8_`) by names (e.g. `.x`, `.y`).  
  
For example, this should be possible:  
  
```  
boost::compute::float4_ v;  
v.x = 1;  
v.y = 2;  
v.z = 3;  
v.w = 4;  
assert(v == boost::compute::float4_(1, 2, 3, 4));  
```  
  
Note that names should only be available if the type actually has enough values (e.g. `float2_` should not have a `.z` field).  
##

---

## Comment 1

> Username: roshanrags  
> Created at: 2014-03-20 00:56:47 UTC  
> Url: https://github.com/boostorg/compute/issues/44#issuecomment-38125368  

Have almost completed support for accessors of the form `.s#`. Will soon extend it to `.x`, `.y`, etc.
