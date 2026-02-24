# #138 - Split Boost.Multiprecision and cpp_int [Closed]

> Username: nemothenoone  
> Created at: 2019-07-13 17:09:32 UTC  
> Updated at: 2019-07-16 12:05:56 UTC  
> Closed at: 2019-07-16 12:05:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/138  

According to an architectural decision of having frontends and backends, it seems to be a fine step to split (at least in some directory-structuring way) the ```number``` frontend, adaptors from the backend implementation.  
  
Since I'm going (step-by-step) to advance the ```cpp_int``` backend to be a non-GPL-licensed long arithmetics implementation (for my own purposes)(it should be so, because of it's presence in boost), this seems to be a reasonable issue to raise.

---

## Comment 1

> Username: jzmaddock  
> Created at: 2019-07-14 08:00:27 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/138#issuecomment-511182054  

Currently we have:  
  
boost/multiprecision/cpp_int.hpp  
  
Main #include for cpp_int types, contains the class definitions but not the algorithms.  
  
boost/multiprecision/cpp_int/*.hpp  
  
The actual implementation of the algorithms and the eval_* methods.  
  
It would be not unreasonable (since it has grown so large) to split the contents of cpp_int.hpp into separate headers under boost/multiprecision/cpp_int/.  
  
If you're thinking of doing that, please submit it as a separate PR before any substantive changes are made.  
  
Thanks!

---

## Comment 2

> Username: nemothenoone  
> Created at: 2019-07-16 12:05:56 UTC  
> Url: https://github.com/boostorg/multiprecision/issues/138#issuecomment-511787882  

Okay. After some research no need for now was spotted. Thanks.
