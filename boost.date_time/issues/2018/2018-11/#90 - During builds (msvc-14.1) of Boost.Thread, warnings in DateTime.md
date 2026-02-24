# #90 - During builds (msvc-14.1) of Boost.Thread, warnings in DateTime [Closed]

> Username: jeking3  
> Created at: 2018-11-19 13:42:57 UTC  
> Updated at: 2018-11-20 14:55:24 UTC  
> Closed at: 2018-11-20 14:55:23 UTC  
> Url: https://github.com/boostorg/date_time/issues/90  

https://www.boost.org/development/tests/develop/output/teeks99-09-p-win2012R2-64on64-thread-msvc-14-1-warnings.html#test_once_lib  
  
```  
test_once_lib  
D:\teeks99-09\run\boost_root\boost/date_time/date.hpp(71): warning C4459: declaration of 'm' hides global declaration  
D:\teeks99-09\run\boost_root\boost/date_time/year_month_day.hpp(36): warning C4459: declaration of 'm' hides global declaration  
D:\teeks99-09\run\boost_root\boost/date_time/gregorian_calendar.ipp(25): warning C4459: declaration of 'm' hides global declaration  
D:\teeks99-09\run\boost_root\boost/date_time/gregorian_calendar.ipp(80): warning C4459: declaration of 'm' hides global declaration  
D:\teeks99-09\run\boost_root\boost/date_time/gregorian_calendar.ipp(119): warning C4459: declaration of 'm' hides global declaration  
```

---

## Comment 1

> Username: jeking3  
> Created at: 2018-11-20 14:55:23 UTC  
> Url: https://github.com/boostorg/date_time/issues/90#issuecomment-440301293  

This is an issue in Boost.Thread, global variables for tests like "m" for a mutex or "x" for an integer are not in an anonymous namespace in the tests.
