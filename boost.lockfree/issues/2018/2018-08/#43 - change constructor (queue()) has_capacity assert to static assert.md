# #43 - change constructor (queue()) has_capacity assert to static assert [Closed]

> Username: bansalnvn  
> Created at: 2018-08-31 04:16:05 UTC  
> Updated at: 2019-12-03 16:57:42 UTC  
> Closed at: 2018-09-01 06:09:22 UTC  
> Url: https://github.com/boostorg/lockfree/issues/43  

https://github.com/boostorg/lockfree/blob/ffd91db4589fda2e5f843d0773dc3325baea88e0/include/boost/lockfree/queue.hpp#L188  
  
This check for construction of the queue, if the capacity has been specified as the template parameter should be moved to static assert rather than having the run time assert.

---

## Comment 1

> Username: timblechmann  
> Created at: 2018-08-31 05:25:00 UTC  
> Url: https://github.com/boostorg/lockfree/issues/43#issuecomment-417555343  

iirc this isn't possible, without `enable_if` tricks, as the static assertion will fire when class is instantiated, not when the class is used. if you find a way, which is not completely ugly, i'm happy to merge a PR, though ...

---

## Comment 2

> Username: bansalnvn  
> Created at: 2018-08-31 11:57:09 UTC  
> Url: https://github.com/boostorg/lockfree/issues/43#issuecomment-417641974  

Yeah, I think currently `std::enable_if` or the `if constexpr` is needed. I will close the ticket for now. Thanks.

---

## Comment 3

> Username: austin-beer  
> Created at: 2019-12-03 16:57:42 UTC  
> Url: https://github.com/boostorg/lockfree/issues/43#issuecomment-561259278  

For the record, this is the commit that changed `BOOST_STATIC_ASSERT()` to `BOOST_ASSERT()` back in 2012: 505c1c04c18939cfd8fbf58386230ee78f4549ad
