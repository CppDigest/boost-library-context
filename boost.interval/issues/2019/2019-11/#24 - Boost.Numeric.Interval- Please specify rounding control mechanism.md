# #24 - Boost.Numeric.Interval: Please specify rounding control mechanism [Closed]

> Username: BastienC09  
> Created at: 2019-11-14 14:56:21 UTC  
> Updated at: 2021-04-04 03:58:20 UTC  
> Closed at: 2021-04-04 03:58:20 UTC  
> Url: https://github.com/boostorg/interval/issues/24  

Hi, I am building an app for IOS (ARM) and I had this issue while compiling:  
**Boost.Numeric.Interval: Please specify rounding control mechanism.**  
  
C99 version should be used in my case but  __USE_ISOC99 is not defined.  
  
Modifying the condition with this code (using __cplusplus) fixed the issue:  
**_boost/numeric/interval/hw_rounding.hpp(36)_**  
  
```  
#if defined(BOOST_NUMERIC_INTERVAL_NO_HARDWARE) && (defined(__USE_ISOC99) || defined(__MSL__) || (defined __cplusplus && __cplusplus >= 201103L))  
 #include <boost/numeric/interval/detail/c99_rounding_control.hpp>  
#endif  
```  
  
Let me know if I missed something or if you plan to integrate this modification in a next version.  
  
Cheers,  
Bastien Commelongue

---

## Comment 1

> Username: afabri  
> Created at: 2021-02-02 12:00:49 UTC  
> Url: https://github.com/boostorg/interval/issues/24#issuecomment-771587032  

@silene is anybody working on a fix  and is what @BastienC09 proposes correct?    I @   you, as you are listed as one of the maintainers.

---

## Comment 2

> Username: silene  
> Created at: 2021-02-02 12:43:11 UTC  
> Url: https://github.com/boostorg/interval/issues/24#issuecomment-771609132  

Sorry, I was not aware of this issue. Thanks for pinging me. Yes, the proposed fix is correct, since C++11 provides C99's `fenv.h`.  
  
I guess that, nowadays, the whole conditional would be changed to something like  
```c++  
#if defined(BOOST_NUMERIC_INTERVAL_NO_HARDWARE) && !defined(BOOST_NO_FENV_H)  
#include <boost/numeric/interval/detail/c99_rounding_control.hpp>  
#endif  
```

---

## Comment 3

> Username: afabri  
> Created at: 2021-02-03 11:05:08 UTC  
> Url: https://github.com/boostorg/interval/issues/24#issuecomment-772425453  

So will you create a pull request?
