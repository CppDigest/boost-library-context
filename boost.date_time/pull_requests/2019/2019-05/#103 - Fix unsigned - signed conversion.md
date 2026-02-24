# #103 Fix unsigned / signed conversion [Merged]

> Username: bansan85  
> Created at: 2019-05-10 07:28:45 UTC  
> Updated at: 2019-05-10 14:32:16 UTC  
> Merged at: 2019-05-10 14:32:16 UTC  
> Closed at: 2019-05-10 14:32:16 UTC  
> Url: https://github.com/boostorg/date_time/pull/103  

gregorian_calendar::end_of_month_day returns unsigned short and date constructor uses unsigned short.  
So eom_day should be unsigned too.  
  
Fix a Visual Studio warning.

---

## Review 1 [Commented]

> Username: JeffGarland  
> Created_at: 2019-05-10 12:23:11 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/date_time/pull/103#pullrequestreview-236074351  

📁 include/boost/date_time/gregorian/greg_date.hpp

```diff
 122 |       ymd_type ymd = year_month_day();
 123 |-       short eom_day =  gregorian_calendar::end_of_month_day(ymd.year, ymd.month);
 123 |+       unsigned short eom_day =  gregorian_calendar::end_of_month_day(ymd.year, ymd.month);
```

> Username: JeffGarland  
> Created_at: 2019-05-10 12:23:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/103#discussion_r282860782  

looks right to me. CI  check issue doesn't seem related...

> Username: JeffGarland  
> Created_at: 2019-05-10 12:24:11 UTC  
> Url: https://github.com/boostorg/date_time/pull/103#discussion_r282861056  

going to be off grid for a few days, will approve after or James can if he thinks its right.


---
