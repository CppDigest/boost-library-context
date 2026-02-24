# #212 Fix typo in time_period end() example [Merged]

> Username: Quinnsicle  
> Created at: 2022-07-01 19:51:52 UTC  
> Updated at: 2022-07-02 17:42:21 UTC  
> Merged at: 2022-07-02 17:41:52 UTC  
> Closed at: 2022-07-02 17:41:52 UTC  
> Url: https://github.com/boostorg/date_time/pull/212  

![image](https://user-images.githubusercontent.com/27598913/176959945-ccfaeb38-6653-468d-98b7-0f1546fcf81c.png)  
  
Highlighted is the typo, this should be tp.end() not tp.last(), as this is an example of the behavior of the end() accessor. I've tested these functions and have confirmed the behavior matches the example.

---

## Comment 1

> Username: JeffGarland  
> Created_at: 2022-07-02 17:42:21 UTC  
> Url: https://github.com/boostorg/date_time/pull/212#issuecomment-1172934284  

Thanks for the patch.

---
