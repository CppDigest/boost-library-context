# #748 - Building on MinGW fails due to missing "std::mutex" [Closed]

> Username: yaronct  
> Created at: 2022-01-26 03:26:47 UTC  
> Updated at: 2022-02-01 19:02:52 UTC  
> Closed at: 2022-02-01 19:02:52 UTC  
> Url: https://github.com/boostorg/math/issues/748  

MinGW-w64 (GCC targetting Windows) that uses the "win32" threading model, doesn't provide some threading stuff like "std::mutex" (I'm not sue about the "posix" threading model), therefore building fails with:  
  
./boost/math/special_functions/detail/bernoulli_details.hpp:563:9: error: ‘mutex’ in namespace ‘std’ does not name a type  
  563 |    std::mutex m_mutex;  
      |         ^~~~~  
  
This can probably be solved by using threading stuff from the boost thread library (in the case of MinGW), as I think boost thread gives an API compatible with that of the standard C++ library ("std"). So I think, e.g. using "boost::mutex" should be compatible with "std::mutex". I haven't tried it though. Alternately, building boost math can be disabled on MinGW (possibly only for the "win32" threading model).  
  
Tested against boost 1.78.0.

---

## Comment 1

> Username: mborland  
> Created at: 2022-01-27 17:30:23 UTC  
> Url: https://github.com/boostorg/math/issues/748#issuecomment-1023471001  

Can you try the linked PR, and let me know if it solves your issue?

---

## Comment 2

> Username: yaronct  
> Created at: 2022-01-31 04:43:10 UTC  
> Url: https://github.com/boostorg/math/issues/748#issuecomment-1025376479  

Thanks, @jzmaddock, #753 solves the problem. Or at least eliminates it :smile:
