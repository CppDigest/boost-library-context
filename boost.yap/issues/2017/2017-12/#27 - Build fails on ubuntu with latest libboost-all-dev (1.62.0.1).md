# #27 - Build fails on ubuntu with latest libboost-all-dev (1.62.0.1) [Closed]

> Username: thetechniclord  
> Created at: 2017-12-11 21:09:04 UTC  
> Updated at: 2018-06-11 17:16:18 UTC  
> Closed at: 2018-02-03 02:58:44 UTC  
> Url: https://github.com/boostorg/yap/issues/27  

I used the following commands to build the project:  
`mkdir build`  
`cd build`  
`cmake ..`  
And get the following error while building the examples:  
`In file included from /home/tekne/Documents/Projects/yap/example/autodiff_library/autodiff.cpp:13:0:  
/home/tekne/Documents/Projects/yap/example/autodiff_library/autodiff.h:11:10: fatal error: boost/serialization/array_wrapper.hpp: No such file or directory  
 #include <boost/serialization/array_wrapper.hpp>  
`  
Including and using the library itself produces no errors. Thanks!

---

## Comment 1

> Username: tzlaine  
> Created at: 2018-02-03 02:58:44 UTC  
> Url: https://github.com/boostorg/yap/issues/27#issuecomment-362771658  

Thanks for letting me know, but this is a Won't-fix, because that file is available in later versions of Boost (at least as early as 1.64, maybe earlier).
