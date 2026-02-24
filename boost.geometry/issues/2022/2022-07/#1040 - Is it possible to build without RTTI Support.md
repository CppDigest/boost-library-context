# #1040 - Is it possible to build without RTTI Support? [Closed]

> Username: jwillikers  
> Created at: 2022-07-18 17:51:31 UTC  
> Updated at: 2022-07-18 17:57:30 UTC  
> Closed at: 2022-07-18 17:57:30 UTC  
> Url: https://github.com/boostorg/geometry/issues/1040  

Is it possible to use Boost.Geometry without requiring RTTI support? There's a `dynamic_cast` [here](https://github.com/boostorg/geometry/blob/070e3517c4ba3e1c897e962a82072cd4b59e7c19/include/boost/geometry/index/detail/rtree/query_iterators.hpp#L250) that makes it impossible to build with RTTI disabled. Thanks!  
  
```  
include/boost/geometry/index/detail/rtree/query_iterators.hpp:250:44: error: ‘dynamic_cast’ not permitted with ‘-fno-rtti’  
  250 |         const query_iterator_wrapper * p = dynamic_cast<const query_iterator_wrapper *>(boost::addressof(r));  
```
