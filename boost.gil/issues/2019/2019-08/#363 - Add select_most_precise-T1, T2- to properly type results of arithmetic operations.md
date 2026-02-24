# #363 - Add select_most_precise<T1, T2> to properly type results of arithmetic operations [Open]

> Username: mloskot  
> Created at: 2019-08-01 16:06:00 UTC  
> Updated at: 2019-08-01 16:06:00 UTC  
> Url: https://github.com/boostorg/gil/issues/363  

(This is follow-up to the discussion on #362, specifically, https://github.com/boostorg/gil/issues/362#issuecomment-517203864, copied below)  
  
### Problem  
  
There is a need of selection of best type for results of arithmetic operations on channels, pixels and other numerical operands, that would also help to avoid any implicit or unexpected conversions and warnings.  
  
### Solutions  
  
@mloskot suggests there are two choices, at least:  
  
1. Use `double` as common type for results of arithmetic operations, then explicitly cast to expected result type. Preferably, with sprinkle of assertions checking the intermediate and final values are in expected range.  
  
2. Select most precise type at compile time. This approach is used in Boost.Geometry, see [geometry::select_most_precise](https://github.com/boostorg/geometry/blob/d3e572ede341191df9a3b751771ace9c3e554364/include/boost/geometry/util/select_most_precise.hpp) metafunction and how it is used in [value_operation](https://github.com/boostorg/geometry/blob/d3e572ede341191df9a3b751771ace9c3e554364/include/boost/geometry/arithmetic/arithmetic.hpp#L46-L68) executor which is called like this:  
  
    ```cpp  
    detail::value_operation<typename coordinate_type<Point>::type, std::divides>(value);  
    ```  
  
### Scope  
  
Areas that could benefit from `select_most_precise`  
  
- [channel_algorithm.hpp](https://github.com/boostorg/gil/blob/5d69ccf987f5b8020007af993c1713052bcdf48a/include/boost/gil/channel_algorithm.hpp) e.g. `channel_multiply`  
- [image_processing/*.hpp](https://github.com/boostorg/gil/tree/5d69ccf987f5b8020007af993c1713052bcdf48a/include/boost/gil/image_processing)  
- [point.hpp](https://github.com/boostorg/gil/blob/5d69ccf987f5b8020007af993c1713052bcdf48a/include/boost/gil/point.hpp) e.g. `operator/`  
- [premultiply.hpp](https://github.com/boostorg/gil/blob/5d69ccf987f5b8020007af993c1713052bcdf48a/include/boost/gil/premultiply.hpp)  
- [extension/numeric/*.hpp](https://github.com/boostorg/gil/tree/5d69ccf987f5b8020007af993c1713052bcdf48a/include/boost/gil/extension/numeric) e.g. channel and pixel numeric operations, convolution and correlation, sampling  
  
### Benefits: warnings clean-up  
  
The warnings clean-up in GIL is not an easy task and we have been approaching it bit by bit. There are still a lot of warnings due to:  
  
1. signed vs unsigned integers as templates parameters  
2. signed vs unsigned mix in bit-wise operations  
3. integer vs float-point in arithmetic operations  
4. other more trivial places like memory elements indexing using ptrdiff_t, coord_t vs unsigned integer, etc.  
  
Some warnings are due to mismatch of local types in narrow, local scope (e.g. a single function) and these are fairly easy to fix.  
Other warnings are due to mismatch of types coming from outside scope, e.g. integer template parameters. Those are the more difficult to fix as avoiding warning for one category of input types may lead to warnings for other category of input types.  
  
A compile-time selection of most precise or largest type of given two would help to fix the issues in cases 1. and 2 at least.  
  
### References  
  
- #204 by @sdebionne   
- [Discussion on Gitter](https://gitter.im/boostorg/gil?at=5d42b8f2a96def47519992ae) with @simmplecoder from August 1, 2019
