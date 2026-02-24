# #47 Add more robust integer overflow handling in month offset algorithms. [Closed]

> Username: Lastique  
> Created at: 2017-09-06 18:31:06 UTC  
> Updated at: 2017-09-06 20:20:45 UTC  
> Closed at: 2017-09-06 20:20:45 UTC  
> Url: https://github.com/boostorg/date_time/pull/47  

The calculated year value can exceed both the allowed year range and the  
year representation type range, which can cause integer overflows when  
the resulting date is constructed. The range checks in the year  
constructor don't help because the new year value can be truncated before  
passing to the constructor due to implicit integer conversion.  
  
This commit performs range checks on the full-width integers before  
calling the year's constructor. This somewhat duplicates the  
constrained_value logic, but it gets the job done.

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-09-06 20:20:45 UTC  
> Url: https://github.com/boostorg/date_time/pull/47#issuecomment-327601678  

This approach was rejected in the discussion in https://github.com/boostorg/date_time/pull/46.

---
