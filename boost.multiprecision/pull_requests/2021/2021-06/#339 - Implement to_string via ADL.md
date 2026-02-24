# #339 Implement to_string via ADL. [Merged]

> Username: NAThompson  
> Created at: 2021-06-21 14:55:44 UTC  
> Updated at: 2021-06-23 14:35:23 UTC  
> Merged at: 2021-06-23 08:08:09 UTC  
> Closed at: 2021-06-23 08:08:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/339  

Solves #97

---

## Comment 1

> Username: NAThompson  
> Created_at: 2021-06-22 14:47:55 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/339#issuecomment-866049264  

@jzmaddock : I admit to not knowing how this should be tested, but otherwise it seems fine. Build error is a timeout.

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2021-06-22 17:05:44 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/339#issuecomment-866168974  

The examples here might make some minimal tests: https://en.cppreference.com/w/cpp/string/basic_string/to_string though the 1e40 example will obviously change it's output depending on the precision of the type.  
  
Can you add some minimal docs to doc/reference_number.qbk ?  
  
My only (slight) concern, is that floating point values with large (64-bit!!) exponent ranges could generate stupidly long strings - something that might warrant a warning in the docs.

---
