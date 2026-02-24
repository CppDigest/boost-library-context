# #14 Fix #11437: correct immediate_rolling_mean [Merged]

> Username: oliness  
> Created at: 2018-02-06 10:42:31 UTC  
> Updated at: 2018-11-21 14:34:23 UTC  
> Merged at: 2018-11-07 00:33:45 UTC  
> Closed at: 2018-11-07 00:33:45 UTC  
> Url: https://github.com/boostorg/accumulators/pull/14  

Corrects the immediate_rolling_mean calculation for unsigned  
int, so that when a value lower than the previous value is added,  
underflow does not occur.  
  
Includes new regression test for this behavior.

---
