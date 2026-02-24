# #254 - CI/faber seems to not run some of the tests [Open]

> Username: Kojoley  
> Created at: 2019-03-22 23:58:24 UTC  
> Updated at: 2019-03-22 23:58:24 UTC  
> Url: https://github.com/boostorg/python/issues/254  

Some tests are failing with b2 while here on CI they are fine:  
  * `injected`  
  * `boost_shared_ptr`  
  * `shared_ptr`  
  * `auto_ptr`  
  * `operators`  
  * `implicit`  
  * `data_members`  
  * `extract`  
  * `numpy~dtype`  
  
You can look at them on regression matrix or here https://travis-ci.com/Kojoley/python/jobs/187163937
