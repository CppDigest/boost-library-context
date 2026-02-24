# #538 Fix non-zero exit code on Jam parsing errors [Merged]

> Username: mloskot  
> Created at: 2020-02-20 20:09:39 UTC  
> Updated at: 2021-10-02 21:13:30 UTC  
> Merged at: 2020-02-22 21:24:07 UTC  
> Closed at: 2020-02-22 21:24:07 UTC  
> Url: https://github.com/boostorg/build/pull/538  

Prevent resetting the non-Zero status returned from yyanyerrors on any parsing errors.  
  
-----  
  
The bug was reported on #boost Slack  
  
------  
  
A basic verification of the fix:  
  
1. `Jamfile` with a broken syntax  
  
![image](https://user-images.githubusercontent.com/80741/75094978-73857880-5590-11ea-81f6-a02088b932e6.png)  
  
2. Before the fix, `b2` returns Zero  
  
![image](https://user-images.githubusercontent.com/80741/75095010-ceb76b00-5590-11ea-8604-c19ea3608b63.png)  
  
3. After the fix, `b2` returns non-Zero  
  
![image](https://user-images.githubusercontent.com/80741/75094985-88620c00-5590-11ea-8f8d-aa4353c55f16.png)

---

## Comment 1

> Username: mloskot  
> Created_at: 2020-02-22 15:24:17 UTC  
> Url: https://github.com/boostorg/build/pull/538#issuecomment-589966269  

My initial attempt to fix this with unconditional ` last_update_now_status = status;` was incorrect.  
  
Since, AFAIU, `last_update_now_status` may be set before `status` is set in line `status = yyanyerrors();`, unconditionally setting the last status with `status` had undesired effects.  
  
I have updated the PR according to my current understanding.

---
