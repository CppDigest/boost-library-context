# #335 Add first tests for convolve functions from Numeric extension [Merged]

> Username: mloskot  
> Created at: 2019-07-13 22:41:23 UTC  
> Updated at: 2020-02-22 13:12:44 UTC  
> Merged at: 2019-07-14 20:43:32 UTC  
> Closed at: 2019-07-14 20:43:32 UTC  
> Url: https://github.com/boostorg/gil/pull/335  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
First stab at convolution tests includes cases with the identity kernel.  
  
Move `pixel`-s streaming facility used by Boost.Test (required  by `BOOST_TEST` macro) to the common header for re-use in other tests.  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve  
  
------  
  
/cc @miralshah365

---
