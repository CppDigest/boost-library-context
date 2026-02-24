# #734 - Compiler warning when copying from std::array. [Open]

> Username: BrettRyland  
> Created at: 2017-07-13 11:45:44 UTC  
> Updated at: 2017-07-13 11:51:00 UTC  
> Url: https://github.com/boostorg/compute/issues/734  

When copying a std::array to a compute::vector, e.g.,  
  
    std::array<cl_double, 3> host_tmp{ 1.0, 2.0, 3.0 };  
    compute::vector<cl_double> device_tmp(host_tmp.size(), context);  
    compute::copy(host_tmp.begin(), host_tmp.end(), device_tmp.begin(), queue);  
  
I get a compiler warning `'argument': conversion from 'size_t' to 'boost::compute::uint_', possible loss of data` coming from lines 332 and 336 of boost\compute\algorithm\copy.hpp, i.e. the lines  
  
    map_copy_threshold =  
        parameters->get(  
            cache_key, "map_copy_threshold", map_copy_threshold  
        );  
    direct_copy_threshold =  
        parameters->get(  
            cache_key, "direct_copy_threshold", direct_copy_threshold  
        );  
  
This doesn't happen if I use a std::vector instead of a std::array.
