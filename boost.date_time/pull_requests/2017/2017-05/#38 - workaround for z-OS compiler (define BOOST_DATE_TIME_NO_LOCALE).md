# #38 workaround for z/OS compiler (define BOOST_DATE_TIME_NO_LOCALE) [Merged]

> Username: pgroke-dt  
> Created at: 2017-05-05 16:15:49 UTC  
> Updated at: 2017-05-08 08:05:39 UTC  
> Merged at: 2017-05-07 00:00:18 UTC  
> Closed at: 2017-05-07 00:00:18 UTC  
> Url: https://github.com/boostorg/date_time/pull/38  

The z/OS compiler has buggy header files where including <cctype> "shadows" the locale-enabled version of some functions defined in <locale>.  
=> Have to define BOOST_DATE_TIME_NO_LOCALE, otherwise things start to go wrong if the user includes <cctype> before including a Boost.DataTime header.

---
