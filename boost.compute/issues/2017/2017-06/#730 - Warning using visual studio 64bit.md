# #730 - Warning using visual studio 64bit [Closed]

> Username: dirkvdb  
> Created at: 2017-06-09 16:21:11 UTC  
> Updated at: 2017-08-15 11:41:28 UTC  
> Closed at: 2017-08-15 11:41:28 UTC  
> Url: https://github.com/boostorg/compute/issues/730  

Boost version 1.64.0  
  
> boost/compute/algorithm/copy.hpp(332): warning C4267: 'argument': conversion from 'size_t' to 'boost::compute::uint_', possible loss of data  
  
In copy.hpp:329  
`map_copy_threshold =  
        parameters->get(  
            cache_key, "map_copy_threshold", map_copy_threshold  
        );`  
The third argument of the get method is a uint_ but the map_copy_threshold is a size_t
