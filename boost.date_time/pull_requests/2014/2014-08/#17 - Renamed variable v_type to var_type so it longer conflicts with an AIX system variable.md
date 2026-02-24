# #17 Renamed variable v_type to var_type so it longer conflicts with an AIX system variable. [Merged]

> Username: ismirlian  
> Created at: 2014-08-08 15:59:20 UTC  
> Updated at: 2014-08-08 16:19:53 UTC  
> Merged at: 2014-08-08 16:16:04 UTC  
> Closed at: 2014-08-08 16:16:04 UTC  
> Url: https://github.com/boostorg/date_time/pull/17  

Do you think this is an acceptable way to handle the change?

---

## Comment 1

> Username: mclow  
> Created_at: 2014-08-08 16:16:00 UTC  
> Url: https://github.com/boostorg/date_time/pull/17#issuecomment-51623239  

Looks great to me.  Please watch the test results, and remind me in about a week to merge this change to the master branch.

---

## Comment 2

> Username: ismirlian  
> Created_at: 2014-08-08 16:19:53 UTC  
> Url: https://github.com/boostorg/date_time/pull/17#issuecomment-51624015  

Ok, I reran the test suite presenting the issue, interprocess_example, and it ran as well with this solution as it had with the previously presented solution, so I think that we are in the clear.

---
