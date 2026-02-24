# #787 fix variable shadowing in check_results function(simple moving average example) [Closed]

> Username: antonmyagkov  
> Created at: 2018-07-13 11:16:31 UTC  
> Updated at: 2018-07-13 13:32:12 UTC  
> Closed at: 2018-07-13 13:32:12 UTC  
> Url: https://github.com/boostorg/compute/pull/787  

Variable shadowing is occurred with bool and float 'ret', as result check_results function always return true

---
