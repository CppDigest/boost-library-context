# #186 - Warning whe compiling with -Wundef on clang [Closed]

> Username: viboes  
> Created at: 2017-09-09 23:30:51 UTC  
> Updated at: 2017-10-25 17:20:23 UTC  
> Closed at: 2017-10-25 17:20:23 UTC  
> Url: https://github.com/boostorg/config/issues/186  

When compiling with -Wundef on clang we get the following warning  
```  
/xxx/modular-boost3/boost/type_traits/is_nothrow_move_constructible.hpp:30:79: warning: 'BOOST_GCC_VERSION_WORKAROUND_GUARD' is not defined, evaluates to 0 [-Werror,-Wundef]  
#elif !defined(BOOST_NO_CXX11_NOEXCEPT) && !defined(BOOST_NO_SFINAE_EXPR) && !BOOST_WORKAROUND(BOOST_GCC_VERSION, < 40800)  
                                                                              ^  
/Users/viboes/github/modular-boost3/boost/config/workaround.hpp:244:10: note: expanded from macro 'BOOST_WORKAROUND'  
       ((symbol ## _WORKAROUND_GUARD + 0 == 0) &&     \  
         ^  
<scratch space>:222:1: note: expanded from here  
BOOST_GCC_VERSION_WORKAROUND_GUARD  
^  
```  
  
This is because `BOOST_GCC_VERSION_WORKAROUND_GUARD` is not defined and used in an expression.  
  
The following fixes the issue.  
  
```  
#ifndef BOOST_GCC  
#define BOOST_GCC_WORKAROUND_GUARD 1  
#define BOOST_GCC_VERSION_WORKAROUND_GUARD 1  
#else  
#define BOOST_GCC_WORKAROUND_GUARD 0  
#define BOOST_GCC_VERSION_WORKAROUND_GUARD 0  
#endif  
``

---

## Comment 1

> Username: eldiener  
> Created at: 2017-10-07 14:01:39 UTC  
> Url: https://github.com/boostorg/config/issues/186#issuecomment-334937162  

I have created a PR for this issue.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2017-10-25 17:20:23 UTC  
> Url: https://github.com/boostorg/config/issues/186#issuecomment-339404602  

Since we have a PR, I'm closing this issue.
