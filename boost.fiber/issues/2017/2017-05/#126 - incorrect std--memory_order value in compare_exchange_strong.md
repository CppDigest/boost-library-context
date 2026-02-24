# #126 - incorrect std::memory_order value in compare_exchange_strong? [Closed]

> Username: nenomius  
> Created at: 2017-05-21 17:41:58 UTC  
> Updated at: 2017-05-26 16:03:34 UTC  
> Closed at: 2017-05-26 16:03:34 UTC  
> Url: https://github.com/boostorg/fiber/issues/126  

[detail/spinlock_ttas_adaptive_futex.hpp#L69](https://github.com/boostorg/fiber/blob/fb3fa3da0e0a54e764de66cc4221879eeeec1a42/include/boost/fiber/detail/spinlock_ttas_adaptive_futex.hpp#L69)  
[detail/spinlock_ttas_futex.hpp#L65](https://github.com/boostorg/fiber/blob/fb3fa3da0e0a54e764de66cc4221879eeeec1a42/include/boost/fiber/detail/spinlock_ttas_futex.hpp#L65)  
[unbuffered_channel.hpp#L242](https://github.com/boostorg/fiber/blob/fb3fa3da0e0a54e764de66cc4221879eeeec1a42/include/boost/fiber/unbuffered_channel.hpp#L242)  
[unbuffered_channel.hpp#L292](https://github.com/boostorg/fiber/blob/fb3fa3da0e0a54e764de66cc4221879eeeec1a42/include/boost/fiber/unbuffered_channel.hpp#L292)  
[unbuffered_channel.hpp#L74](https://github.com/boostorg/fiber/blob/fb3fa3da0e0a54e764de66cc4221879eeeec1a42/include/boost/fiber/unbuffered_channel.hpp#L74)  
[unbuffered_channel.hpp#L89](https://github.com/boostorg/fiber/blob/fb3fa3da0e0a54e764de66cc4221879eeeec1a42/include/boost/fiber/unbuffered_channel.hpp#L89)  
  
http://en.cppreference.com/w/cpp/atomic/atomic/compare_exchange  
> failure - the memory synchronization ordering for the load operation if the comparison fails. **Cannot be std::memory_order_release or std::memory_order_acq_rel** [and cannot specify stronger ordering than success (until C++17)]

---

## Comment 1

> Username: olk  
> Created at: 2017-05-26 16:03:34 UTC  
> Url: https://github.com/boostorg/fiber/issues/126#issuecomment-304321528  

unbuffered_channel is correct - only the spinlocks have to be corrected
