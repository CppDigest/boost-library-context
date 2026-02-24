# #145 - Allocations not handled correctly in some cases of vector move with unequal allocators [Closed]

> Username: ergpudb  
> Created at: 2020-04-14 15:33:52 UTC  
> Updated at: 2020-05-24 22:35:24 UTC  
> Closed at: 2020-05-24 22:35:24 UTC  
> Url: https://github.com/boostorg/container/issues/145  

When move assigning or move constructing a vector from another vector with an unequal, non-propagating allocator, there are cases where memory ends up not being allocated from the correct allocators and the system ends up in an invalid state.  
  
The attached reproducer demonstrates this for two test cases. Both use a vector<vector<int>> with a scoped allocator that counts allocations and deallocations. The first test case uses move assignment, the second uses move construction with specified allocator. In both cases, using boost vectors, the allocators end up in a nonzero state when all vectors are destroyed. Using STL vectors in gcc 5 w/ C++11 ABI (and presumably newer versions), the allocators end up in a zero state at the end as they should.  
  
[test.cpp.txt](https://github.com/boostorg/container/files/4476206/test.cpp.txt)

---

## Comment 1

> Username: igaztanaga  
> Created at: 2020-05-24 22:29:04 UTC  
> Url: https://github.com/boostorg/container/issues/145#issuecomment-633309902  

Many thanks for the report. There is a bug on vector's extended move constructor which considers passed allocator equal to the internal one. Fixing it and improving tests to catch these type of errors.
