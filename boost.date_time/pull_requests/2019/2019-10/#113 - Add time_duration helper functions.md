# #113 Add time_duration helper functions: [Merged]

> Username: gawain-bolton  
> Created at: 2019-10-31 21:00:48 UTC  
> Updated at: 2019-11-01 11:08:46 UTC  
> Merged at: 2019-11-01 11:08:46 UTC  
> Closed at: 2019-11-01 11:08:46 UTC  
> Url: https://github.com/boostorg/date_time/pull/113  

1. is_positive()  
  - Return boolean value to indicate whether or not time duration is  
    positive.  
2. is_zero()  
  - Return boolean value to indicate whether or not time duration is  
    zero.  
3. abs()  
  - Return a time_duration which is the absolute value of time  
    duration.  
  
Added documentation for these helper functions and improved existing  
documentation to indicate constness, return values or static  
functions.

---

## Comment 1

> Username: gawain-bolton  
> Created_at: 2019-11-01 07:50:22 UTC  
> Url: https://github.com/boostorg/date_time/pull/113#issuecomment-548698548  

Hello Jeff,  
Thank you for approving these changes.  
However, unless I am mistaken it seems the pull request has been closed without merging the commit.   Is there a reason for this?

---

## Comment 2

> Username: JeffGarland  
> Created_at: 2019-11-01 10:08:46 UTC  
> Updated_at: 2019-11-01 10:09:29 UTC  
> Url: https://github.com/boostorg/date_time/pull/113#issuecomment-548735192  

Sorry my bad -- from earlier email traffic and what interface was showing me I mistakenly thought it had been merged.  Looks like the travis ci hasn't completed yet so will do after that's run (not sure why its taking so long -- maybe bc I closed)...

---
