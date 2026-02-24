# #525 Vector with custom allocator fix [Merged]

> Username: jszuppe  
> Created at: 2015-10-05 12:26:56 UTC  
> Updated at: 2015-10-20 21:17:05 UTC  
> Merged at: 2015-10-10 16:16:11 UTC  
> Closed at: 2015-10-10 16:16:11 UTC  
> Url: https://github.com/boostorg/compute/pull/525  

See https://github.com/boostorg/compute/issues/523.   
  
In the first commit I added test for vector's move constructor when custom allocator is used (in that case `boost::computer::pinned_allocator<T>`).  You can checkout to this commit and see that test fails on `BOOST_CHECK(a.size() == 0)` and `BOOST_CHECK(a.get_buffer().get() == 0)` as the copy constructor is used instead of the move ctor.  It also crashes because of some memory access violation that I can not locate right now.  
  
Second commit adds more tests for vector when used with custom allocator.  
  
The last commit includes fixes @pisto presented in https://github.com/boostorg/compute/issues/523 + I added `queue` parameter to copy constructor and a copy constructor for situations when different allocators are used.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2015-10-10 16:16:12 UTC  
> Url: https://github.com/boostorg/compute/pull/525#issuecomment-147103129  

Awesome! Thanks for fixing this!

---
