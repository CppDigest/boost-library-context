# #34 Fix result_of usage [Merged]

> Username: morinmorin  
> Created at: 2017-09-11 16:14:07 UTC  
> Updated at: 2017-09-16 07:59:21 UTC  
> Merged at: 2017-09-11 20:44:37 UTC  
> Closed at: 2017-09-11 20:44:37 UTC  
> Url: https://github.com/boostorg/iterator/pull/34  

Since `Function` is called as an lvalue in `function_input_iterator`, the correct usage of `result_of` in C++11 is `result_of<Function&()>`.  
  
If the TR1-style `result_of` is used (rather than decltype-based `result_of`), we use `result_of<Function()>` instead. This is because `result_of<Function&()>` is not a standard/advertised usage of the TR1-style `result_of`. (There exists a very rare case where `result_of<Function&()>` is necessary though, e.g. when `Function` is an abstract type; users are required to use decltype-based `result_of` for such cases.)

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-09-11 16:39:26 UTC  
> Url: https://github.com/boostorg/iterator/pull/34#issuecomment-328586702  

Maybe you could include `is_function.hpp` only when needed?

---

## Comment 2

> Username: morinmorin  
> Created_at: 2017-09-11 17:57:25 UTC  
> Url: https://github.com/boostorg/iterator/pull/34#issuecomment-328608262  

Done and squashed.

---
