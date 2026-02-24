# #329 [gsoc2019] Add color_spaces_are_compatible requirement as static_assert to threshold [Merged]

> Username: mloskot  
> Created at: 2019-07-10 04:14:23 UTC  
> Updated at: 2020-02-22 13:11:19 UTC  
> Merged at: 2019-07-10 10:20:42 UTC  
> Closed at: 2019-07-10 10:20:42 UTC  
> Url: https://github.com/boostorg/gil/pull/329  

Since use of `gil_function_requires` check is optional, it can be disabled by not defining `BOOST_GIL_USE_CONCEPT_CHECK`, we need a mandatory form of the check.  
  
Closes #323  
  
### Tasklist  
  
- [x] Add compile-time tests  
- [x] Ensure all CI builds pass  
  
------  
  
/cc @miralshah365

---
