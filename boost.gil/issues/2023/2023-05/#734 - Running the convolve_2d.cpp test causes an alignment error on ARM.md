# #734 - Running the convolve_2d.cpp test causes an alignment error on ARM [Open]

> Username: dkrejsa  
> Created at: 2023-05-08 00:17:59 UTC  
> Updated at: 2023-05-08 00:42:34 UTC  
> Url: https://github.com/boostorg/gil/issues/734  

### Actual behavior  
  
<!-- A clear description of what happens -->  
  
When running the convolve_2d.cpp test module (Boost 1.81.0) on a 32-bit ARM target, there is an alignment  
exception (fault) in the `test_convolve_2d_with_image_using_float32_t()` test case.  The alignment exception  
occurs when the `convolve_2d_impl()` function attempts to access the first pixel in the second row of  
the `src_view`.  The address computed for `src_view(col_boundary, row_boundary)[0]`  is not a multiple of  
the size of the `gil::float32_t` array element.  
  
The apparent cause is that the test case creates the `gil::interleaved_view()` calls for the source and output  
images use the number of columns in the row rather than the length in bytes of each row as the  
last argument to `gill::interleaved_view()`.  Likely  
  
```  
gil::gray32fc_view_t src_view =  
        gil::interleaved_view(9, 9, reinterpret_cast<gil::gray32f_pixel_t const*>(img), 9);  
gil::gray32fc_view_t exp_out_view =  
        gil::interleaved_view(9, 9, reinterpret_cast<gil::gray32f_pixel_t const*>(exp_output), 9);  
```  
should be  
```  
gil::gray32fc_view_t src_view =  
        gil::interleaved_view(9, 9, reinterpret_cast<gil::gray32f_pixel_t const*>(img), 9 * sizeof (gil::float32_t));  
gil::gray32fc_view_t exp_out_view =  
        gil::interleaved_view(9, 9, reinterpret_cast<gil::gray32f_pixel_t const*>(exp_output), 9 * sizeof (gil::float32_t));  
```  
  
### Expected  behavior  
  
<!-- A clear and concise description of what you expected to happen. -->  
  
The test should not cause an alignment exception and should complete successfully.  
  
### C++ Minimal Working Example  
  
Use the convolve_2d.cpp test case on a target that gives exceptions for misaligned accesses.  
  
### Environment  
  
<!-- Any details that may help GIL maintainers help you -->  
  
- Compiler version:  
- Build settings:  
- Version (Git ref or `<boost/version.hpp>`):
