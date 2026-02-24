# #38 - Fail to compile circular_buffer/test/common.ipp with C++20 since std::allocator::max_size is removed in C++20 [Closed]

> Username: yuxianch  
> Created at: 2021-01-21 06:14:56 UTC  
> Updated at: 2021-02-16 03:34:19 UTC  
> Closed at: 2021-02-16 03:34:19 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/38  

circular_buffer/test/common.ipp is calling std::allocator::max_size, which is [removed in C++20](https://en.cppreference.com/w/cpp/memory/allocator/max_size).  
https://github.com/boostorg/circular_buffer/blob/2b15dc6044ed5cec0f7422a073280d234e0b4e00/test/common.ipp#L250-L251  
  
Need to make change to common.ipp so that it can conform to C++20 standard.

---

## Comment 1

> Username: glenfe  
> Created at: 2021-02-16 03:34:18 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/38#issuecomment-779558853  

Should be fixed in the next release.
