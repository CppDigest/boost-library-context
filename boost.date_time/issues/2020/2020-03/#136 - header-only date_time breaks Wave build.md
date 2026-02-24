# #136 - header-only date_time breaks Wave build [Closed]

> Username: jefftrull  
> Created at: 2020-03-15 22:46:56 UTC  
> Updated at: 2020-03-16 05:27:42 UTC  
> Closed at: 2020-03-16 04:04:58 UTC  
> Url: https://github.com/boostorg/date_time/issues/136  

Wave, and perhaps other Boost libraries, attempt to link against date_time. New CI builds for those libraries are failing. Could there be a provision for a helpful error message, or perhaps a "stub" library similar to the one provided for System?

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-03-16 00:55:21 UTC  
> Url: https://github.com/boostorg/date_time/issues/136#issuecomment-599292310  

yep, ok @pdimov must made the same point -- I can do something there.  Honestly I thought I'd searched boost for date_time references -- apparently I missed with my regex.

---

## Comment 2

> Username: JeffGarland  
> Created at: 2020-03-16 02:04:45 UTC  
> Url: https://github.com/boostorg/date_time/issues/136#issuecomment-599304974  

pull request #137 -- waiting for CI to merge to develop

---

## Comment 3

> Username: JeffGarland  
> Created at: 2020-03-16 04:04:57 UTC  
> Url: https://github.com/boostorg/date_time/issues/136#issuecomment-599334898  

resolved

---

## Comment 4

> Username: jefftrull  
> Created at: 2020-03-16 05:27:42 UTC  
> Url: https://github.com/boostorg/date_time/issues/136#issuecomment-599351996  

Thanks Jeff! I took them out of Wave but that will help others.
