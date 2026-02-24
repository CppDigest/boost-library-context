# #202 - `boost/timer.hpp` is deprecated [Closed]

> Username: NAThompson  
> Created at: 2019-02-01 20:48:07 UTC  
> Updated at: 2019-02-27 23:50:09 UTC  
> Closed at: 2019-02-27 23:49:58 UTC  
> Url: https://github.com/boostorg/test/issues/202  

```  
In file included from test/condition_number_test.cpp:13:  
In file included from ../test/include/boost/test/included/unit_test.hpp:22:  
In file included from ../test/include/boost/test/impl/framework.ipp:50:  
../timer/include/boost/timer.hpp:21:1: warning: This header is deprecated. Use the facilities in <boost/timer/timer.hpp> instead. [-W#pragma-messages]  
BOOST_HEADER_DEPRECATED( "the facilities in <boost/timer/timer.hpp>" )  
```  
  
I apologize for not just fixing this.   
  
Commit hash of test: 489499c8402cf74d1704824d055011cb1223a689  
Commit hash of timer:  075d6b25ab185526b0704fc9d1c982931729c35c

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-02-01 20:49:27 UTC  
> Url: https://github.com/boostorg/test/issues/202#issuecomment-459863031  

Don't be sorry, I learned `BOOST_HEADER_DEPRECATED`!

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-02-27 20:41:40 UTC  
> Url: https://github.com/boostorg/test/issues/202#issuecomment-468021959  

I ended up refactoring completely the timed execution of boost.test.

---

## Comment 3

> Username: raffienficiaud  
> Created at: 2019-02-27 23:49:58 UTC  
> Url: https://github.com/boostorg/test/issues/202#issuecomment-468077674  

In master, closing.
