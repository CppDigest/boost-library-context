# #740 - Algorithms do not heed the maximum work-group size of the device [Open]

> Username: doe300  
> Created at: 2017-09-01 11:18:43 UTC  
> Updated at: 2018-12-27 11:29:55 UTC  
> Url: https://github.com/boostorg/compute/issues/740  

Almost all algorithms in boost compute, which set an explicit local work-group size do not check if this size exceeds the device maximum work-group size. Instead they all set it to a default value of 128 (e.g. `algorithm/detail/binary_find.hpp` in line 82).  
  
Thus, most of the algorithms and therefore the Boost.Compute test-cases fail for OpenCL devices with a maximum work-group size of less then 128.  
  
So, the default value should be checked against the device's maximum work-group size, which already can be retrieved via `boost::compute::device::max_work_group_size()`.
