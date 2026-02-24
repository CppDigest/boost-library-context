# #8 #else/#endif comments are updated accordingly to the used condition [Merged]

> Username: mingulov  
> Created at: 2014-03-24 06:48:43 UTC  
> Updated at: 2014-07-23 05:05:24 UTC  
> Merged at: 2014-03-25 15:05:14 UTC  
> Closed at: 2014-03-25 15:05:14 UTC  
> Url: https://github.com/boostorg/date_time/pull/8  

Fixed incorrect comments for the condition at the next line:  
# if defined(BOOST_DATE_TIME_HAS_REENTRANT_STD_FUNCTIONS)  
  
There were later:  
# else // BOOST_HAS_THREADS  
# endif // BOOST_HAS_THREADS  
  
It prevented a proper fast undesrstanding of the source.

---
