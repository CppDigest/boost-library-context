# #353 Add more Thrust performance benchmarks [Merged]

> Username: kylelutz  
> Created at: 2014-12-20 21:56:36 UTC  
> Updated at: 2014-12-21 17:34:37 UTC  
> Merged at: 2014-12-21 17:34:35 UTC  
> Closed at: 2014-12-21 17:34:35 UTC  
> Url: https://github.com/boostorg/compute/pull/353  



---

## Comment 1

> Username: coveralls  
> Created_at: 2014-12-20 22:17:10 UTC  
> Url: https://github.com/boostorg/compute/pull/353#issuecomment-67751930  

[![Coverage Status](https://coveralls.io/builds/1641880/badge)](https://coveralls.io/builds/1641880)  
  
Coverage remained the same when pulling **e0688c53e54cb0c5a09f39697e125009e5f276a9 on more-thrust-benchmarks** into **7c957831c3ed602389ea385e8a4db23eca820beb on develop**.

---

## Comment 2

> Username: ddemidov  
> Created_at: 2014-12-21 13:34:06 UTC  
> Url: https://github.com/boostorg/compute/pull/353#issuecomment-67770550  

I've updated plots at https://gist.github.com/ddemidov/192740d6e0d4871b9d40,  
but I get an `out of resources` error on `set_difference` benchmark (Boost.Compute part of it):  
  
```  
$ ../perf/perfdoc.py   
running 'accumulate'  
running 'count'  
running 'inner_product'  
running 'merge'  
running 'partial_sum'  
running 'partition'  
running 'reverse'  
running 'rotate'  
running 'saxpy'  
running 'set_difference'  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
terminate called recursively  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
terminate called recursively  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::context_error> >'  
terminate called recursively  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
terminate called recursively  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
  what():  Unknown OpenCL Error (-9999)  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
terminate called recursively  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
  what():  Unknown OpenCL Error (-9999)  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
  what():  Out of Resources  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
  what():  Unknown OpenCL Error (-9999)  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
terminate called recursively  
terminate called after throwing an instance of 'boost::exception_detail::clone_impl<boost::exception_detail::error_info_injector<boost::compute::opencl_error> >'  
terminate called recursively  
  what():  Out of Resources  
running 'sort'  
running 'unique'  
```

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-12-21 17:01:34 UTC  
> Url: https://github.com/boostorg/compute/pull/353#issuecomment-67776680  

Thanks for running these! I'll look into the `set_difference` error more. @roshanr95 any ideas about that? Can you try running the `perfdoc.py` script locally and see if you can reproduce it?

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-12-21 17:25:45 UTC  
> Updated_at: 2014-12-21 17:26:00 UTC  
> Url: https://github.com/boostorg/compute/pull/353#issuecomment-67777394  

I've updated the `perfdoc.py` script to not run the `set_difference` benchmark. I'll re-enable it once we get it working again.

---
