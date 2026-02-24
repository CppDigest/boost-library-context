# #129 Do not include same header twice [Merged]

> Username: shinobu-x  
> Created at: 2017-08-14 22:34:38 UTC  
> Updated at: 2017-08-16 05:41:24 UTC  
> Merged at: 2017-08-16 05:41:24 UTC  
> Closed at: 2017-08-16 05:41:24 UTC  
> Url: https://github.com/boostorg/thread/pull/129  

Remove <boost/assert.hpp>, since this header was included twice.  
File: include/boost/thread/pthread/shared_mutex.hpp

---
