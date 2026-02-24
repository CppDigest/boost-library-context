# #232 - Broken on g++ [Closed]

> Username: pdimov  
> Created at: 2018-09-12 12:49:06 UTC  
> Updated at: 2018-10-09 04:34:38 UTC  
> Closed at: 2018-09-15 16:00:16 UTC  
> Url: https://github.com/boostorg/thread/issues/232  

https://travis-ci.org/boostorg/boost/builds/426912551  
  
```  
./boost/thread/detail/thread_safety.hpp:74:62: error: missing binary operator before token "("  
 #if defined(__clang__) && (!defined(SWIG)) && __has_attribute(no_thread_safety_analysis) && defined(__FreeBSD__)  
                                                              ^  
```

---

## Comment 1

> Username: viboes  
> Created at: 2018-09-15 04:09:14 UTC  
> Url: https://github.com/boostorg/thread/issues/232#issuecomment-421529003  

Sorry, I believed that the expression would be evaluated only if `__clang__` was defined

---

## Comment 2

> Username: viboes  
> Created at: 2018-09-15 08:21:14 UTC  
> Url: https://github.com/boostorg/thread/issues/232#issuecomment-421540672  

https://github.com/boostorg/thread/commit/92a5bc43003db14000b7f4d51c88bd4164258456

---

## Comment 3

> Username: pdimov  
> Created at: 2018-09-15 16:00:16 UTC  
> Url: https://github.com/boostorg/thread/issues/232#issuecomment-421591336  

Looks fixed: https://travis-ci.org/boostorg/boost/builds/428926383
