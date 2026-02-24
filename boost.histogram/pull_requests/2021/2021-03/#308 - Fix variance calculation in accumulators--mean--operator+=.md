# #308 Fix variance calculation in accumulators::mean::operator+= [Merged]

> Username: HDembinski  
> Created at: 2021-03-16 11:00:33 UTC  
> Updated at: 2021-03-16 12:17:44 UTC  
> Merged at: 2021-03-16 12:17:40 UTC  
> Closed at: 2021-03-16 12:17:40 UTC  
> Url: https://github.com/boostorg/histogram/pull/308  

The variance calculation of accumulators::mean::operator+= was broken. This was not caught by the corresponding test, since the test only checked the case when the two summed means were identical, where the faulty implementation happened to give the right answer.  
  
The problem is fixed and a new test was added to check that the sum of two different mean accumulator states gives the same outcome as filling one accumulator with the data of both.

---
