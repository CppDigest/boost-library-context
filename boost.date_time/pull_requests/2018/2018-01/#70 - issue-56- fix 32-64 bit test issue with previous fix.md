# #70 issue-56: fix 32/64 bit test issue with previous fix [Merged]

> Username: jeking3  
> Created at: 2018-01-07 16:24:09 UTC  
> Updated at: 2018-01-11 00:08:51 UTC  
> Merged at: 2018-01-07 21:24:22 UTC  
> Closed at: 2018-01-07 21:24:22 UTC  
> Url: https://github.com/boostorg/date_time/pull/70  

The test was not accounting for binary size differences in the 32/64 bit outputs.  
  
Caused by:  
  
https://github.com/boostorg/date_time/pull/58

---
