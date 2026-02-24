# #17 fix a bug in time_get<>::get_day_year_num() [Merged]

> Username: rick68  
> Created at: 2016-06-26 19:11:39 UTC  
> Updated at: 2016-06-27 23:23:54 UTC  
> Merged at: 2016-06-27 23:23:24 UTC  
> Closed at: 2016-06-27 23:23:24 UTC  
> Url: https://github.com/boostorg/chrono/pull/17  

`boost::chrono::detail::time_get<>::get()` with format command `%j` is ranged from 000 to 365, but the definition in `std::strftime()` is from 001 to 366

---

## Comment 1

> Username: viboes  
> Created_at: 2016-06-27 23:23:54 UTC  
> Url: https://github.com/boostorg/chrono/pull/17#issuecomment-228905480  

Thanks for the patch. I believe that I will need a test :(

---
