# #323 Add extents<N> class [Merged]

> Username: kylelutz  
> Created at: 2014-12-03 05:43:21 UTC  
> Updated at: 2014-12-04 07:26:08 UTC  
> Merged at: 2014-12-04 07:26:06 UTC  
> Closed at: 2014-12-04 07:26:06 UTC  
> Url: https://github.com/boostorg/compute/pull/323  

This adds an extents<N> class which stores an array of n-dimensional  
extent values. This can be used to represent an ND-range for launching  
an OpenCL kernel or for storing the size/shape of a 2D/3D image.  
  
Also adds a variadic dim() function which provides a concise syntax  
for creating extents<N> objects.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-12-03 05:59:46 UTC  
> Url: https://github.com/boostorg/compute/pull/323#issuecomment-65359833  

[![Coverage Status](https://coveralls.io/builds/1558577/badge)](https://coveralls.io/builds/1558577)  
  
Coverage remained the same when pulling **5bca5ccdf9e4903cae8b4b0105ae714bc8680739 on extents** into **bb19d2a07c3598dbdfe4e4be535ea5282dab4e35 on develop**.

---

## Comment 2

> Username: coveralls  
> Created_at: 2014-12-03 07:58:22 UTC  
> Url: https://github.com/boostorg/compute/pull/323#issuecomment-65367813  

[![Coverage Status](https://coveralls.io/builds/1558795/badge)](https://coveralls.io/builds/1558795)  
  
Coverage remained the same when pulling **4185694366f154340b0eb52901c5567f4639df1a on extents** into **bb19d2a07c3598dbdfe4e4be535ea5282dab4e35 on develop**.

---
