# #157 Add benchmarks for partitioning algorithms [Merged]

> Username: roshanrags  
> Created at: 2014-06-21 22:29:54 UTC  
> Updated at: 2014-06-26 23:22:36 UTC  
> Merged at: 2014-06-25 01:52:11 UTC  
> Closed at: 2014-06-25 01:52:11 UTC  
> Url: https://github.com/boostorg/compute/pull/157  

Added benchmarks for the partitioning algorithms. Also, changed `competitiors` in `perf.py` to have a vertical layout to make it look better as more benchmarks are added...

---

## Comment 1

> Username: roshanrags  
> Created_at: 2014-06-21 22:46:18 UTC  
> Url: https://github.com/boostorg/compute/pull/157#issuecomment-46766956  

Uh, a small problem. Some stuff is only available in c++11 (like partition_point). How do I handle this? `#ifdef`s?

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-06-22 17:29:26 UTC  
> Url: https://github.com/boostorg/compute/pull/157#issuecomment-46787136  

I added a `BOOST_COMPUTE_USE_CPP11` CMake flag (commit here: 049fbd1a36a10d5ef921c00961f47b24e672846b).  
  
So in `perf/CMakeList.txt`, you should be able to do something like this now (after rebasing on `develop`):  
  
```  
if(${BOOST_COMPUTE_USE_CPP11})                                                                                                                                                                                                                                                   
  list(APPEND                                                                                                                                                                                                                                                                    
    STL_BENCHMARKS                                                                                                                                                                                                                                                               
    stl_partition                                                                                                                                                                                                                                                                
    stl_partition_point                                                                                                                                                                                                                                                          
  )                                                                                                                                                                                                                                                                              
endif()  
```

---

## Comment 3

> Username: roshanrags  
> Created_at: 2014-06-24 23:31:51 UTC  
> Url: https://github.com/boostorg/compute/pull/157#issuecomment-47043933  

Done..

---

## Comment 4

> Username: coveralls  
> Created_at: 2014-06-24 23:37:51 UTC  
> Url: https://github.com/boostorg/compute/pull/157#issuecomment-47044363  

[![Coverage Status](https://coveralls.io/builds/900578/badge)](https://coveralls.io/builds/900578)  
  
Coverage remained the same when pulling **2e825a9ad13be9d3cbf33f9f8c5b237c5538c060 on roshanr95:partitioning_algorithms** into **049fbd1a36a10d5ef921c00961f47b24e672846b on kylelutz:develop**.

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-06-25 01:52:12 UTC  
> Url: https://github.com/boostorg/compute/pull/157#issuecomment-47052300  

Looks good! Merged!

---
