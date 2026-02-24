# #138 - Wrong docu for LazyInit of intermodule_singleton [Closed]

> Username: Flamefire  
> Created at: 2021-02-08 09:20:36 UTC  
> Updated at: 2021-02-11 23:12:09 UTC  
> Closed at: 2021-02-11 23:12:09 UTC  
> Url: https://github.com/boostorg/interprocess/issues/138  

Compare https://github.com/boostorg/interprocess/blob/286ef46560e36d67b86ecbef5e24cb6a56e311f1/include/boost/interprocess/detail/intermodule_singleton.hpp#L35-L38  
  
vs  
  
https://github.com/boostorg/interprocess/blob/8f63c6054ca437dacc9a6c073806f66222bdd943/include/boost/interprocess/detail/intermodule_singleton_common.hpp#L323-L326  
  
In the first the docu has swapped true/false
