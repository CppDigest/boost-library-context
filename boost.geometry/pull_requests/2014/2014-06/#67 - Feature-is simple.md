# #67 Feature/is simple [Merged]

> Username: mkaravel  
> Created at: 2014-06-19 13:41:36 UTC  
> Updated at: 2014-06-22 20:32:42 UTC  
> Merged at: 2014-06-22 20:32:42 UTC  
> Closed at: 2014-06-22 20:32:42 UTC  
> Url: https://github.com/boostorg/geometry/pull/67  

Optimize the calls to self turns. To do this, pass a predicate-based interrupt policy to self turns that checks each turn as to whether it is acceptable or not.  
  
Two new policies have been introduced (in detail::overlay namespace):  
1. predicate_based_interrupt_policy: takes a predicate object at construction time and uses it to check whether turns are acceptable or not.  
2. stateless_predicate_based_interrupt_policy: takes a predicate type and uses its static apply method to check whether turns are acceptable or not.

---
