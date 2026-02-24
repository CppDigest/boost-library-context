# #362 - generate_gaussian_kernel and warning C4244: conversion from double to float [Open]

> Username: mloskot  
> Created at: 2019-07-31 22:48:38 UTC  
> Updated at: 2019-08-01 09:24:14 UTC  
> Url: https://github.com/boostorg/gil/issues/362  

@simmplecoder Would it be possible to get rid of these warnings?  
```  
image_processing\numeric.hpp(104): warning C4244: 'initializing': conversion from 'double' to 'float', possible loss of data  
image_processing\numeric.hpp(104): warning C4244: 'initializing': conversion from 'double' to 'const float', possible loss of data  
```  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version: `_MSC_VER=1922`

---

## Comment 1

> Username: simmplecoder  
> Created at: 2019-08-01 03:48:29 UTC  
> Url: https://github.com/boostorg/gil/issues/362#issuecomment-517107333  

I was thinking about making the argument a template, after adding `boost::gil::gray64f_view_t` and tests. Casting after making a template might suppress warnings that would otherwise be useful. Do other Boost libraries handle such issues?

---

## Comment 2

> Username: mloskot  
> Created at: 2019-08-01 09:23:23 UTC  
> Updated at: 2019-08-01 09:24:14 UTC  
> Url: https://github.com/boostorg/gil/issues/362#issuecomment-517203864  

The questions are related to the issue #204 raised by @sdebionne   
  
------  
/cc @stefanseefeld   
  
I think there are two choices at least:  
  
1. Use `double` as common type for results of arithmetic operations, then explicitly cast to expected result type. Preferably, with sprinkle of assertions checking the intermediate and final values are in expected range.  
  
2. Select most precise type at compile time. This approach is used in Boost.Geometry, see [geometry::select_most_precise](https://github.com/boostorg/geometry/blob/d3e572ede341191df9a3b751771ace9c3e554364/include/boost/geometry/util/select_most_precise.hpp) metafunction and how it is used in [value_operation](https://github.com/boostorg/geometry/blob/d3e572ede341191df9a3b751771ace9c3e554364/include/boost/geometry/arithmetic/arithmetic.hpp#L46-L68) executor which is called like this:  
  
    ```cpp  
    detail::value_operation<typename coordinate_type<Point>::type, std::divides>(value);  
    ```  
  
    I imagine, we could employ similar approach.  
  
  
If we consider 2. to be a compelling idea, then we can leave this issue open and the current code  (as reminder) until we have PR ready with `gil::select_most_precise` added and applied to channel and pixel arithmetic.
