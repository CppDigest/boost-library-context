# #524 Vector with custom allocator fix [Closed]

> Username: jszuppe  
> Created at: 2015-10-05 12:24:51 UTC  
> Updated at: 2015-10-05 12:25:14 UTC  
> Closed at: 2015-10-05 12:25:14 UTC  
> Url: https://github.com/boostorg/compute/pull/524  

See https://github.com/boostorg/compute/issues/523.   
  
In the first commit I added test for vector's move constructor when custom allocator is used (in that case `boost::computer::pinned_allocator<T>`).  You can checkout to this commit and see that test fails on `BOOST_CHECK(a.size() == 0)` and `BOOST_CHECK(a.get_buffer().get() == 0)` as the copy constructor is used instead of the move ctor.  It also crashes because of some memory access violation that I can not locate right now.  
  
Second commit adds more tests for vector when used with custom allocator.  
  
The last commit includes fixes @pisto presented in https://github.com/boostorg/compute/issues/523 + I added `queue` parameter to copy constructor and a copy constructor for situations when different allocators are used.

---
