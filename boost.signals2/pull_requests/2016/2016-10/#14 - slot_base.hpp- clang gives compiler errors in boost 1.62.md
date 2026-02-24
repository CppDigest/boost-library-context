# #14 slot_base.hpp: clang gives compiler errors in boost 1.62 [Merged]

> Username: jaredgrubb  
> Created at: 2016-10-01 21:56:11 UTC  
> Updated at: 2016-10-01 23:21:01 UTC  
> Merged at: 2016-10-01 23:21:00 UTC  
> Closed at: 2016-10-01 23:21:00 UTC  
> Url: https://github.com/boostorg/signals2/pull/14  

I am trying to update to boost-1.62 but am hitting compiler errors:  
  
/usr/local/include/boost/signals2/slot_base.hpp:70:29: error: expected value in expression  
    #if(!BOOST_NO_EXCEPTIONS)  
                            ^

---
