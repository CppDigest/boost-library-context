# #18 fix a bug in time_get<>::get_month() [Merged]

> Username: rick68  
> Created at: 2016-06-26 22:54:07 UTC  
> Updated at: 2016-06-28 12:16:30 UTC  
> Merged at: 2016-06-28 12:16:30 UTC  
> Closed at: 2016-06-28 12:16:30 UTC  
> Url: https://github.com/boostorg/chrono/pull/18  

According to `boost::chrono::detail::time_get<>::get()` with format command `%m`, the valid range is from `01` to `12`, it should not allow passing `00` and assign `-1` into m.

---

## Comment 1

> Username: viboes  
> Created_at: 2016-06-27 23:25:30 UTC  
> Url: https://github.com/boostorg/chrono/pull/18#issuecomment-228905794  

Could you add a test?

---
