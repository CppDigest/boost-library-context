# #55 - Bug in reduce_on_gpu.hpp [Closed]

> Username: youngtaekoh  
> Created at: 2014-02-14 18:50:17 UTC  
> Updated at: 2014-02-15 02:16:51 UTC  
> Closed at: 2014-02-15 02:16:51 UTC  
> Url: https://github.com/boostorg/compute/issues/55  

_gid_ (reduce_on_gpu.hpp:38) must be commented out to avoid "unused variable" warning by OpenCL compiler.

---

## Comment 1

> Username: kylelutz  
> Created at: 2014-02-15 02:16:51 UTC  
> Url: https://github.com/boostorg/compute/issues/55#issuecomment-35144934  

Thanks for the report!  
  
Fixed in e7a76c343aa54d0196ce5ff65f61ae1e66526763.
