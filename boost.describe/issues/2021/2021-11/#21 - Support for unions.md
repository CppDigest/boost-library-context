# #21 - Support for unions [Closed]

> Username: pdimov  
> Created at: 2021-11-12 19:30:11 UTC  
> Updated at: 2021-12-15 02:40:22 UTC  
> Closed at: 2021-12-15 02:40:22 UTC  
> Url: https://github.com/boostorg/describe/issues/21  

`BOOST_DESCRIBE_STRUCT` and `BOOST_DESCRIBE_CLASS` reject unions with a `static_assert`, but they shouldn't.  
  
However, once unions are allowed, all the examples need an `is_class` check added, because traversing all the described members of a union is guaranteed undefined behavior (because at most one is active.)  
  
https://lists.boost.org/Archives/boost/2021/11/252270.php

---

## Comment 1

> Username: pdimov  
> Created at: 2021-12-05 17:20:00 UTC  
> Url: https://github.com/boostorg/describe/issues/21#issuecomment-986268271  

The prototype implementation of the Reflection TS supports unions: https://compiler-explorer.com/z/q9TEGE598
