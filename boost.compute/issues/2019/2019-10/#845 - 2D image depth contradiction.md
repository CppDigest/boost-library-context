# #845 - 2D image depth contradiction [Open]

> Username: etomzak  
> Created at: 2019-10-15 14:48:46 UTC  
> Updated at: 2019-10-15 14:48:46 UTC  
> Url: https://github.com/boostorg/compute/issues/845  

`include/boost/compute/image/image2d.hpp` defines `image_depth = 1`, but `test/test_image2d.cpp` (`get_info`) tests that `CL_IMAGE_DEPTH` is `0`. I'm not sure exactly what this test is trying to do, but I believe `0` is correct [[source](https://www.khronos.org/registry/OpenCL/specs/opencl-1.2.pdf) S 5.3.7].
