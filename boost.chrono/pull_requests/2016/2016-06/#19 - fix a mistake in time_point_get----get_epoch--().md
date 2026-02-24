# #19 fix a mistake in time_point_get<>::get_epoch<>() [Merged]

> Username: rick68  
> Created at: 2016-06-29 03:57:53 UTC  
> Updated at: 2016-07-01 23:26:59 UTC  
> Merged at: 2016-07-01 23:26:59 UTC  
> Closed at: 2016-07-01 23:26:59 UTC  
> Url: https://github.com/boostorg/chrono/pull/19  

It invokes member function `get_epoch<Clock>()` without the template argument `Clock`.

---
