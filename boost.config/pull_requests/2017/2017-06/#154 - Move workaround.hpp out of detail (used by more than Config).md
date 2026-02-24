# #154 Move workaround.hpp out of detail (used by more than Config) [Merged]

> Username: glenfe  
> Created at: 2017-06-12 12:05:14 UTC  
> Updated at: 2017-06-13 17:51:01 UTC  
> Merged at: 2017-06-13 17:51:00 UTC  
> Closed at: 2017-06-13 17:51:00 UTC  
> Url: https://github.com/boostorg/config/pull/154  

Still keep the detail header for compatibility, but other libraries should probably be including <boost/config/workaround.hpp> since it's not really an implementation details specific to Config.

---
