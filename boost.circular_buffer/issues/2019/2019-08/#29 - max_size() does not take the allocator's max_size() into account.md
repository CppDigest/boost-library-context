# #29 - [Regression] max_size() does not take the allocator's max_size() into account [Closed]

> Username: alexkaratarakis  
> Created at: 2019-08-15 19:12:29 UTC  
> Updated at: 2019-08-16 01:49:24 UTC  
> Closed at: 2019-08-16 01:49:24 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/29  

Boost >=1.69 implementation of [`max_size()`](https://github.com/boostorg/circular_buffer/blob/boost-1.69.0/include/boost/circular_buffer/base.hpp#L790) calls [this](https://github.com/boostorg/circular_buffer/blob/boost-1.69.0/include/boost/circular_buffer/allocators.hpp#L37) function which does NOT take into account the maximum size of the allocator.  
  
Boost <= 1.68 implementation of [`max_size()`](https://github.com/boostorg/circular_buffer/blob/boost-1.68.0/include/boost/circular_buffer/base.hpp#L790)  was calling [this](https://github.com/boostorg/container/blob/boost-1.68.0/include/boost/container/allocator_traits.hpp#L327) function from `boost::container` which does take into account the maximum size of the allocator.  
  
The relevant commit is ca3d6678ee565c0adaf03d04afa080a94891a01e @glenfe   
  
As a result, when using a custom allocator with limited size `MY_MAX_SIZE`, previous calls to `max_size()` would return `MY_MAX_SIZE`, but they will now return `(std::numeric_limits<size_type>::max)() / sizeof(value_type)`

---

## Comment 1

> Username: glenfe  
> Created at: 2019-08-15 19:41:36 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/29#issuecomment-521770461  

I'll take care of it for Boost 1.72

---

## Comment 2

> Username: glenfe  
> Created at: 2019-08-15 21:30:12 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/29#issuecomment-521804600  

@alexkaratarakis 7be41ca8f9d94bf7d6f612f43be9d1e6a61348a1 should address this. We're almost ready to ship 1.71, so the earliest it will be released is 1.72.

---

## Comment 3

> Username: alexkaratarakis  
> Created at: 2019-08-15 23:54:08 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/29#issuecomment-521836697  

Thanks for the swift response and fix!
