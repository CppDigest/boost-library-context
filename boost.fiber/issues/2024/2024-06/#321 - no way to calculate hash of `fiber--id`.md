# #321 - no way to calculate hash of `fiber::id` [Closed]

> Username: anton-potapov  
> Created at: 2024-06-25 15:31:02 UTC  
> Updated at: 2024-12-26 07:22:00 UTC  
> Closed at: 2024-12-26 07:22:00 UTC  
> Url: https://github.com/boostorg/fiber/issues/321  

There should be a way to calculate hash over `fiber::id` (just like for [`std::thread::id`](https://en.cppreference.com/w/cpp/thread/thread/id/hash)).    
`fiber::id` has a pointer to context, which can be used to calculate hash.  
  
This can significantly simplify changing porting code from `std::thread` to `boost::fibers`
