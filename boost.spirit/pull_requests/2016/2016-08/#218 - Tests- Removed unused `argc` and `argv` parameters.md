# #218 Tests: Removed unused `argc` and `argv` parameters [Merged]

> Username: Kojoley  
> Created at: 2016-08-29 16:28:45 UTC  
> Updated at: 2017-11-12 22:02:21 UTC  
> Merged at: 2016-08-29 23:46:52 UTC  
> Closed at: 2016-08-29 23:46:52 UTC  
> Url: https://github.com/boostorg/spirit/pull/218  

Fixes following warnings:  
  
```  
karma_regression_real_0-p3  
..\libs\spirit\test\karma\regression_real_0.cpp:15:15: warning: unused parameter 'argc' [-Wunused-parameter]  
..\libs\spirit\test\karma\regression_real_0.cpp:15:28: warning: unused parameter 'argv' [-Wunused-parameter]  
  
regression_less_8563-p3  
..\libs\spirit\test\lex\regression_less_8563.cpp:30:14: warning: unused parameter 'argc' [-Wunused-parameter]  
..\libs\spirit\test\lex\regression_less_8563.cpp:30:31: warning: unused parameter 'argv' [-Wunused-parameter]  
  
regression_static_wide_6253-p3  
..\libs\spirit\test\lex\regression_static_wide_6253.cpp:31:14: warning: unused parameter 'argc' [-Wunused-parameter]  
..\libs\spirit\test\lex\regression_static_wide_6253.cpp:31:31: warning: unused parameter 'argv' [-Wunused-parameter]  
  
```

---
