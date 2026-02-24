# #226 - Wrong namespace order in numa example code [Closed]

> Username: ingomueller-net  
> Created at: 2020-01-03 19:43:49 UTC  
> Updated at: 2020-01-08 09:18:12 UTC  
> Closed at: 2020-01-08 09:18:12 UTC  
> Url: https://github.com/boostorg/fiber/issues/226  

The code that illustrates the usage of the NUMA work-stealing algorithms [in the docs](https://www.boost.org/doc/libs/1_72_0/libs/fiber/doc/html/fiber/numa.html#fiber.numa.numa_support_in_boost_fiber) currently loosk like this:  
  
```c++  
boost::fibers::use_scheduling_algorithm< boost::fibers::algo::numa::work_stealing >( cpu_id, node_id, topo);  
// plus another similar occurrence further down  
```  
  
Note that the namespace is `algo::numa` where it should be `numa::algo`.
