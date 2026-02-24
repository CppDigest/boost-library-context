# #796 - rtree/test_rtree.hpp uses std::allocator rebind deprecated in C++17 and removed in C++20 [Open]

> Username: yuxianch  
> Created at: 2021-01-18 06:00:36 UTC  
> Updated at: 2021-01-19 14:35:19 UTC  
> Url: https://github.com/boostorg/geometry/issues/796  

rebind in std::allocator is deprecated in C++17 and removed in C++20.   
See https://en.cppreference.com/w/cpp/memory/allocator.  
  
This change makes that the tests that include test_rtree.hpp are failed in C++20 since rebind is used in serveral lines of test_rtree.hpp.   
https://github.com/boostorg/geometry/blob/18f6ad3658b987ff96e8e2b2d2a752584e5e74cb/index/test/rtree/test_rtree.hpp#L1697  
  
The use of rebind in test_rtree.hpp needs to be modified to conform to C++20.

---

## Comment 1

> Username: mloskot  
> Created at: 2021-01-18 10:44:11 UTC  
> Url: https://github.com/boostorg/geometry/issues/796#issuecomment-762160953  

@yuxianch  Pull requests are always welcome
