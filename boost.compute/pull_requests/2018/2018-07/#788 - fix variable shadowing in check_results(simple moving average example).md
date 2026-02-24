# #788 fix variable shadowing in check_results(simple moving average example) [Merged]

> Username: antonmyagkov  
> Created at: 2018-07-13 13:34:35 UTC  
> Updated at: 2018-12-27 16:39:34 UTC  
> Merged at: 2018-12-27 16:39:33 UTC  
> Closed at: 2018-12-27 16:39:34 UTC  
> Url: https://github.com/boostorg/compute/pull/788  

Variable shadowing is occurred with bool and float 'ret', as result check_results function always return true

---

## Comment 1

> Username: coveralls  
> Created_at: 2018-12-27 16:38:03 UTC  
> Url: https://github.com/boostorg/compute/pull/788#issuecomment-450186670  

[![Coverage Status](https://coveralls.io/builds/20814492/badge)](https://coveralls.io/builds/20814492)  
  
Coverage remained the same at 82.683% when pulling **217f77aa20d2ee24bbc50d14caa544ed11087b7f on u-s:bugfix/variable-shadowing-simple-moving-average** into **be85263cc1ba22679e8c8feb49fee1932729f80b on boostorg:develop**.

---
