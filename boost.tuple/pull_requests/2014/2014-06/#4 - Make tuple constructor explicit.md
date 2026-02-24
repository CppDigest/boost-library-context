# #4 Make tuple constructor explicit [Merged]

> Username: MarcelRaad  
> Created at: 2014-06-03 12:33:14 UTC  
> Updated at: 2014-06-17 10:19:20 UTC  
> Merged at: 2014-06-03 15:36:47 UTC  
> Closed at: 2014-06-03 15:36:47 UTC  
> Url: https://github.com/boostorg/tuple/pull/4  

Otherwise everything implicitly convertible to the data type of the first element is implicitly convertible to a boost::tuple, which has led to long-unnoticed bugs in production code. This change is also consistent with the TR1 and C++11 tuple.

---
