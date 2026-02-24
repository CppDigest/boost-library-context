# #44 - compile error on C++20 [Closed]

> Username: stream009  
> Created at: 2020-08-02 10:23:10 UTC  
> Updated at: 2020-08-02 10:26:59 UTC  
> Closed at: 2020-08-02 10:26:15 UTC  
> Url: https://github.com/boostorg/signals2/issues/44  

I got following compile error when I compile boost signals2 with -std=c++2a.  
Reason for error is `std::allocator::pointer` is deprecated in C++17 and removed in C++20.  
```  
/usr/include/boost/signals2/connection.hpp|53 col 61| required from here  
/usr/include/boost/signals2/detail/auto_buffer.hpp|145 col 58| error: no type named ‘pointer’ in ‘class std::allocator<boost::shared_ptr<void> >’  
||   145 |         typedef typename Allocator::pointer              allocator_pointer;  
||       |                                                          ^~~~~~~~~~~~~~~~~  
```

---

## Comment 1

> Username: stream009  
> Created at: 2020-08-02 10:26:58 UTC  
> Url: https://github.com/boostorg/signals2/issues/44#issuecomment-667656123  

I'm sorry. I notice it is already fixed in recent version.
