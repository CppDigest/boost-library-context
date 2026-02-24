# #193 - -DBOOST_COBALT_NO_PMR=1 breaks MSVC build [Closed]

> Username: francoisk  
> Created at: 2024-07-22 12:15:01 UTC  
> Updated at: 2024-07-22 13:10:49 UTC  
> Closed at: 2024-07-22 13:10:49 UTC  
> Url: https://github.com/boostorg/cobalt/issues/193  

The MSVC build fails with the following error if `-DBOOST_COBALT_NO_PMR=1` is defined:  
```  
src\detail\util.cpp(23): error C2039: 'get_allocator': is not a member of 'boost::cobalt::this_thread'  
src\detail\util.cpp(23): error C3861: 'get_allocator': identifier not found  
```  
  
As can be seen in the code snippets below, `boost::cobalt::this_thread::get_allocator()` is not declared if `BOOST_COBALT_NO_PMR` is defined, yet it is called unconditionally under MSVC.  
  
https://github.com/boostorg/cobalt/blob/af72ced0518b350044c35f8aca8232aa2c0e79c3/include/boost/cobalt/this_thread.hpp#L20-L24  
  
https://github.com/boostorg/cobalt/blob/af72ced0518b350044c35f8aca8232aa2c0e79c3/include/boost/cobalt/config.hpp#L29-L32  
  
https://github.com/boostorg/cobalt/blob/af72ced0518b350044c35f8aca8232aa2c0e79c3/src/detail/util.cpp#L17-L28
