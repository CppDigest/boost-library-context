# #627 - BOOST_COMPUTE_ADAPT_STRUCT results in C4800 (VS2013) [Closed]

> Username: Naviee  
> Created at: 2016-07-09 14:43:34 UTC  
> Updated at: 2016-07-09 15:02:55 UTC  
> Closed at: 2016-07-09 15:02:52 UTC  
> Url: https://github.com/boostorg/compute/issues/627  

Whenever I try to compile a struct that I adapted with BOOST_COMPUTE_ADAPT_STRUCT and that struct has an array as a member, the VS2013 compiler gives a warning of type C4800: "int\* " forcing value to bool 'true' or 'false' (performance warning).  
  
e.g.  
  
```  
struct test  
{  
  int x[3];  
}  
  
BOOST_COMPUTE_ADAPT_STRUCT(test,mytest,(x)) //<-C4800  
```  
  
This is quite annoying.

---

## Comment 1

> Username: jszuppe  
> Created at: 2016-07-09 14:56:32 UTC  
> Url: https://github.com/boostorg/compute/issues/627#issuecomment-231538328  

I agree, VS warnings are annoying. You can add option `/wd4800` to your build or you can do this:  
  
``` cpp  
#include <boost/compute/detail/diagnostic.hpp>  
  
struct test  
{  
  int x[3];  
}  
  
BOOST_COMPUTE_MSVC_DIAG_OFF(4800);  
BOOST_COMPUTE_ADAPT_STRUCT(test,mytest,(x));  
BOOST_COMPUTE_MSVC_DIAG_ON(4800);  
```

---

## Comment 2

> Username: jszuppe  
> Created at: 2016-07-09 14:57:55 UTC  
> Url: https://github.com/boostorg/compute/issues/627#issuecomment-231538400  

In fact, I recommend disabling C4244, C4305, C4800 and C4838.

---

## Comment 3

> Username: Naviee  
> Created at: 2016-07-09 15:02:52 UTC  
> Url: https://github.com/boostorg/compute/issues/627#issuecomment-231538632  

Thanks and Ok. Yeah, I've already disabled the warning (will do for the other suggested too once I looked them up) but I thought I'd issue it anyway.  
  
Cheers
