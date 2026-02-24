# #134 - make date_time all inline [Closed]

> Username: JeffGarland  
> Created at: 2020-03-14 23:15:20 UTC  
> Updated at: 2020-05-04 16:04:39 UTC  
> Closed at: 2020-05-04 16:04:39 UTC  
> Url: https://github.com/boostorg/date_time/issues/134  

This change completely removes the library from date-time in all compiler modes from 98 to 20.  In large part the library remains feature intact with the exception of the removal of the pre 133 facet support (see issue #61).  This brings a number of benefits:  
  
- no need for users to build and link library  
- gregorian and posix_time are no longer dependent on shared_ptr  
- test code is radically simplified and executes faster  
- facilitates constexpr support ( also now added - see issue #123 )  
  
The test code has been updated to minimize support on legacy, non streams based io.  In addition, all the variations of dll versus static are no longer required which reduces the number of tests and the time to run.

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-05-04 16:04:39 UTC  
> Url: https://github.com/boostorg/date_time/issues/134#issuecomment-623554116  

This change shipped in boost 1.73.
