# #42 - timegm [Open]

> Username: MBkkt  
> Created at: 2019-08-25 11:27:59 UTC  
> Updated at: 2019-09-14 13:12:23 UTC  
> Url: https://github.com/boostorg/chrono/issues/42  

Why is there no something like `boost::chrono::*something*::timegm(std::tm *)`?  
At the same time, chrono use `timegm` in `time_point_io.hpp`  
  
If there are no objective reasons for this, I would like to write it.

---

## Comment 1

> Username: viboes  
> Created at: 2019-08-26 05:15:07 UTC  
> Url: https://github.com/boostorg/chrono/issues/42#issuecomment-524718571  

Please, could you elaborate?

---

## Comment 2

> Username: MBkkt  
> Created at: 2019-08-26 07:14:45 UTC  
> Updated at: 2019-08-26 09:14:11 UTC  
> Url: https://github.com/boostorg/chrono/issues/42#issuecomment-524746215  

I mean, it would be convenient if boost had the cross-platform function timegm (http://man7.org/linux/man-pages/man3/timegm.3.html, https://docs.microsoft.com/en-us/cpp/c-runtime-library/reference/mkgmtime-mkgmtime32-mkgmtime64?view=vs-2019). Especially if it is used in the boost https://github.com/boostorg/chrono/blob/develop/include/boost/chrono/io/time_point_io.hpp#L1166  
One of my colleagues made a bug due to the fact that the standard library does not have timegm

---

## Comment 3

> Username: MBkkt  
> Created at: 2019-08-26 14:54:25 UTC  
> Url: https://github.com/boostorg/chrono/issues/42#issuecomment-524893219  

`boost::posix_time::to_time_t(boost::posix_time::ptime_from_tm(tm_time))`  
Sorry, for example, such code works correctly, but in general it may be worthwhile to achieve some kind of consistency

---

## Comment 4

> Username: viboes  
> Created at: 2019-08-26 19:38:12 UTC  
> Url: https://github.com/boostorg/chrono/issues/42#issuecomment-525000320  

Ok, i'll consider a pr with tests and doc
