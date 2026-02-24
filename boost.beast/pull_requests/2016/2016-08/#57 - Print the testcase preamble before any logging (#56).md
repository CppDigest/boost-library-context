# #57 Print the testcase preamble before any logging (#56) [Merged]

> Username: nbougalis  
> Created at: 2016-08-20 02:17:17 UTC  
> Updated at: 2016-08-26 17:43:08 UTC  
> Merged at: 2016-08-26 17:43:08 UTC  
> Closed at: 2016-08-26 17:43:08 UTC  
> Url: https://github.com/boostorg/beast/pull/57  



---

## Comment 1

> Username: codecov-io  
> Created_at: 2016-08-20 06:04:02 UTC  
> Updated_at: 2016-08-26 15:04:23 UTC  
> Url: https://github.com/boostorg/beast/pull/57#issuecomment-241181940  

## [Current coverage](https://codecov.io/gh/vinniefalco/Beast/pull/57?src=pr) is 98.06% (diff: 100%)  
  
> Merging [#57](https://codecov.io/gh/vinniefalco/Beast/pull/57?src=pr) into [master](https://codecov.io/gh/vinniefalco/Beast/branch/master?src=pr) will not change coverage  
  
``` diff  
@@             master        #57   diff @@  
==========================================  
  Files            71         71            
  Lines          4036       4036            
  Methods           0          0            
  Messages          0          0            
  Branches          0          0            
==========================================  
  Hits           3958       3958            
  Misses           78         78            
  Partials          0          0            
```  
  
> Powered by [Codecov](https://codecov.io?src=pr). Last update [3ff56eb...037d521](https://codecov.io/gh/vinniefalco/Beast/compare/3ff56eb0711603fd425405d66642764420d44363...037d52114affb4417cae78198b35b2fb09f39773?src=pr)

---

## Comment 2

> Username: coveralls  
> Created_at: 2016-08-20 06:04:15 UTC  
> Url: https://github.com/boostorg/beast/pull/57#issuecomment-241181944  

[![Coverage Status](https://coveralls.io/builds/7533421/badge)](https://coveralls.io/builds/7533421)  
  
Coverage remained the same at 98.067% when pulling **047f1f5265f4259f6261cdc2a89eca58585cdc40 on nbougalis:suite** into **3ff56eb0711603fd425405d66642764420d44363 on vinniefalco:master**.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2016-08-26 12:00:25 UTC  
> Url: https://github.com/boostorg/beast/pull/57#issuecomment-242716094  

If you want GitHub to automatically close the associated issue you need to write `(fix #56)` in the commit message. See:  
https://help.github.com/articles/closing-issues-via-commit-messages/

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2016-08-26 14:18:25 UTC  
> Updated_at: 2016-08-26 14:33:49 UTC  
> Url: https://github.com/boostorg/beast/pull/57#discussion_r76425808  

This isn't called

---

## Comment 5

> Username: vinniefalco  
> Created_at: 2016-08-26 14:18:44 UTC  
> Updated_at: 2016-08-26 14:33:49 UTC  
> Url: https://github.com/boostorg/beast/pull/57#discussion_r76425859  

no spaces before open parenthesis, unless in a math formula

---

## Comment 6

> Username: vinniefalco  
> Created_at: 2016-08-26 14:19:36 UTC  
> Updated_at: 2016-08-26 14:33:49 UTC  
> Url: https://github.com/boostorg/beast/pull/57#discussion_r76426003  

Please reformat:  
  
```  
os_ << suite_results_.name <<  
    (case_results_.name.empty() ? "" :  
        (" " + case_results_.name)) << std::endl;  
```

---

## Comment 7

> Username: coveralls  
> Created_at: 2016-08-26 14:52:10 UTC  
> Url: https://github.com/boostorg/beast/pull/57#issuecomment-242757514  

[![Coverage Status](https://coveralls.io/builds/7625247/badge)](https://coveralls.io/builds/7625247)  
  
Coverage remained the same at 98.067% when pulling **037d52114affb4417cae78198b35b2fb09f39773 on nbougalis:suite** into **3ff56eb0711603fd425405d66642764420d44363 on vinniefalco:master**.

---

## Comment 8

> Username: coveralls  
> Created_at: 2016-08-26 15:04:16 UTC  
> Url: https://github.com/boostorg/beast/pull/57#issuecomment-242760929  

[![Coverage Status](https://coveralls.io/builds/7625393/badge)](https://coveralls.io/builds/7625393)  
  
Coverage remained the same at 98.067% when pulling **037d52114affb4417cae78198b35b2fb09f39773 on nbougalis:suite** into **3ff56eb0711603fd425405d66642764420d44363 on vinniefalco:master**.

---
