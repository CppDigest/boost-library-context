# #167 Add check for packed structs in BOOST_COMPUTE_ADAPT_STRUCT() [Closed]

> Username: kylelutz  
> Created at: 2014-07-03 05:54:21 UTC  
> Updated at: 2014-07-07 15:02:39 UTC  
> Closed at: 2014-07-03 06:24:39 UTC  
> Url: https://github.com/boostorg/compute/pull/167  

This adds a compile-time check for non-padded structs in the  
`BOOST_COMPUTE_ADAPT_STRUCT()` macro. Also updates the macro to  
add `__attribute__((packed))` to the OpenCL struct definition.

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-07-03 07:01:04 UTC  
> Url: https://github.com/boostorg/compute/pull/167#issuecomment-47873303  

[![Coverage Status](https://coveralls.io/builds/928206/badge)](https://coveralls.io/builds/928206)  
  
Coverage remained the same when pulling **235261a97759471a131d35b7a84b3be11963e1b5 on struct-is-packed** into **bfe2bdd7f77644350d76f39a0be49feff79653a9 on master**.

---
