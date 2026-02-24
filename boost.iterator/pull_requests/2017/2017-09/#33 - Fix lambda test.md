# #33 Fix lambda test [Merged]

> Username: morinmorin  
> Created at: 2017-09-07 11:33:14 UTC  
> Updated at: 2017-09-08 15:13:40 UTC  
> Merged at: 2017-09-07 15:36:10 UTC  
> Closed at: 2017-09-07 15:36:10 UTC  
> Url: https://github.com/boostorg/iterator/pull/33  

The lambda test fails on some compilers. This PR disables the test on compilers that do not have enough features.  
- TR1-style decltype does not support C++11 lambdas. They need decltype-based result_of (which is used when `BOOST_NO_CXX11_DECLTYPE_N3276` is not defined) to deduce the type.  
  
Drive by fix: suppress signed-unsigned comparison warning.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-09-07 15:22:44 UTC  
> Url: https://github.com/boostorg/iterator/pull/33#issuecomment-327833548  

Maybe define `BOOST_RESULT_OF_USE_DECLTYPE` instead?

---

## Comment 2

> Username: morinmorin  
> Created_at: 2017-09-07 15:33:08 UTC  
> Updated_at: 2017-09-07 15:33:25 UTC  
> Url: https://github.com/boostorg/iterator/pull/33#issuecomment-327836657  

> Maybe define `BOOST_RESULT_OF_USE_DECLTYPE` instead?  
  
`#ifndef BOOST_NO_CXX11_DECLTYPE` and `#BOOST_RESULT_OF_USE_DECLTYPE` maybe work fine, but I think that `result_of` with that situation is not tested well so I took a safer way.

---

## Comment 3

> Username: morinmorin  
> Created_at: 2017-09-08 14:22:51 UTC  
> Url: https://github.com/boostorg/iterator/pull/33#issuecomment-328116766  

Did you mean  
```  
#include <boost/config.hpp>  
#if !defined(BOOST_NO_CXX11_LAMBDAS) && !defined(BOOST_NO_CXX11_AUTO_DECLARATIONS) \  
 && !defined(BOOST_NO_CXX11_DECLTYPE)  
#  define BOOST_RESULT_OF_USE_DECLTYPE  
#endif  
...  
```  
or  
```  
#if !defined(BOOST_NO_CXX11_LAMBDAS) && !defined(BOOST_NO_CXX11_AUTO_DECLARATIONS) \  
 && defined(BOOST_RESULT_OF_USE_DECLTYPE)  
    // test the iterator with lambda expressions  
...  
```  
?  
  
I thought that you mean the former, but if you meant the latter the I'm going to change the code to use that code.  
  
...Oh, you already improved the code!   
  
- I think it's OK to change `!defined(BOOST_NO_CXX11_DECLTYPE_N3276) || defined(BOOST_RESULT_OF_USE_DECLTYPE)` to `defined(BOOST_RESULT_OF_USE_DECLTYPE)`.  
  
- I forgot to suppress signed-unsigned comparison warning at L104:  
`BOOST_TEST_EQ(generated.size(), 10); // -> 10u`.  
  
Thanks.

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-09-08 14:43:55 UTC  
> Url: https://github.com/boostorg/iterator/pull/33#issuecomment-328122344  

Initially I wanted the former, only in Jamfile. But apparently `BOOST_RESULT_OF_USE_DECLTYPE` makes `result_of` use `decltype` even if it is not supported at all, which breaks C++03 compilers. It is kind of complicated to test for C++11 in Jamfile, so for now I simply added a PP check, just to allow the code to be tested if the user defines `BOOST_RESULT_OF_USE_DECLTYPE` himself. (I do it in my projects, f. ex.)  
  
I guess, I could define the macro in the .cpp file instead. It is probably a good idea to give the iterator+lambda case more testing.  
  
> I think it's OK to change `!defined(BOOST_NO_CXX11_DECLTYPE_N3276) || defined(BOOST_RESULT_OF_USE_DECLTYPE)` to `defined(BOOST_RESULT_OF_USE_DECLTYPE)`.  
  
No, I don't think so. `BOOST_RESULT_OF_USE_DECLTYPE` is a user's config macro, it is not defined by default.

---

## Comment 5

> Username: morinmorin  
> Created_at: 2017-09-08 14:49:39 UTC  
> Url: https://github.com/boostorg/iterator/pull/33#issuecomment-328123915  

> No, I don't think so. `BOOST_RESULT_OF_USE_DECLTYPE` is a user's config macro, it is not defined by default.  
  
For compilers with `!defined(BOOST_NO_CXX11_DECLTYPE_N3276)`, `BOOST_RESULT_OF_USE_DECLTYPE` is defined by default.

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-09-08 15:00:25 UTC  
> Url: https://github.com/boostorg/iterator/pull/33#issuecomment-328127166  

Oh, `result_of.hpp` defines it itself. I didn't know that.

---

## Comment 7

> Username: morinmorin  
> Created_at: 2017-09-08 15:11:54 UTC  
> Url: https://github.com/boostorg/iterator/pull/33#issuecomment-328130640  

> Oh, `result_of.hpp` defines it itself.  
  
Yep, because of that, decltype-based `result_of` is used by default on modern compilers ;)

---

## Comment 8

> Username: Lastique  
> Created_at: 2017-09-08 15:13:40 UTC  
> Url: https://github.com/boostorg/iterator/pull/33#issuecomment-328131245  

Ok, I've updated develop.

---
