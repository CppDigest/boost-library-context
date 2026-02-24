# #201 - handler_type deprecation [Closed]

> Username: yowidin  
> Created at: 2019-05-14 16:30:20 UTC  
> Updated at: 2020-01-13 07:44:27 UTC  
> Closed at: 2020-01-13 07:44:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/201  

As of Boost 1.70.0 the `handler_type` type trait is now removed (it was deprecated before) so the ASIO-based examples no longer compile:  
> The previously deprecated handler_type trait and single-argument form of async_result have now been removed.   
  
Can you please update the yield examples to use `async_result` instead?  
  
I tried to fix the `detail/yield.hpp` definitions myself by I honestly can't handle this level of template programming magic.

---

## Comment 1

> Username: Warchant  
> Created at: 2019-05-18 19:18:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/201#issuecomment-493700671  

After trial and errors I've come up with this:   
https://gist.github.com/Warchant/d58dc2aecc500ee5fce71ff92a2ba094  
  
More experienced boost devs may use this as a reference, and then submit a PR to close this issue.

---

## Comment 2

> Username: olk  
> Created at: 2020-01-13 07:44:27 UTC  
> Url: https://github.com/boostorg/fiber/issues/201#issuecomment-573542530  

fixed
