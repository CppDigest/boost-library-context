# #844 - Too much precision expected of OpenCL `length()` function [Open]

> Username: etomzak  
> Created at: 2019-10-15 14:37:11 UTC  
> Updated at: 2019-10-15 14:37:11 UTC  
> Url: https://github.com/boostorg/compute/issues/844  

The `test_closure::triangle_area` and `test_extrema::max_vector_length` (and possibly others) expect too much precision of the OpenCL built-in `length()` function when calculating the lengths of vectors.  
  
The precision of `length()` is defined in ULP as `<= 3 + 0.5 * ((0.5 * n) + 0.5 * (n - 1))` where `n` is the number of vector elements. [[source](https://www.khronos.org/registry/OpenCL/specs/2.2/html/OpenCL_C.html#relative-error-as-ulps)] For the `float4`s used in `triangle_area`, this works out to 4.75 ULP. Given the expected result is `1.0f`, the actual answer should be approximately `1.0 +/- 5.7e-07` [1]. The precision expected by the test is `1.0e-6` *percent*, or `1.0e-8`.  
  
That is, the `triangle_area` test can fail even on a conformant OpenCL implementation. The `max_vector_length` test has a similar problem.  
  
[1] This doesn't take into account the division by 2 in the test, which introduces further error.
