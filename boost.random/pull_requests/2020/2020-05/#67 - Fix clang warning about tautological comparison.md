# #67 Fix clang warning about tautological comparison. [Closed]

> Username: Lastique  
> Created at: 2020-05-07 12:04:54 UTC  
> Updated at: 2020-07-07 23:25:36 UTC  
> Closed at: 2020-07-07 23:25:36 UTC  
> Url: https://github.com/boostorg/random/pull/67  

Use a more straightforward check for a negative value to silence clang warning:  
  
```  
../../../boost/random/linear_congruential.hpp:140:20: warning: overlapping comparisons always evaluate to false [-Wtautological-overlap-compare]  
        if(_x <= 0 && _x != 0) {  
           ~~~~~~~~^~~~~~~~~~  
```  
  
[Here](https://travis-ci.org/github/boostorg/integer/jobs/683556924#L2076) is an example.

---
