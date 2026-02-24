# #320 [numeric] std::extent not usable to obtain size of kernel_1d_fixed [Merged]

> Username: mloskot  
> Created at: 2019-07-03 13:24:34 UTC  
> Updated at: 2019-07-04 18:45:39 UTC  
> Merged at: 2019-07-04 18:45:34 UTC  
> Closed at: 2019-07-04 18:45:34 UTC  
> Url: https://github.com/boostorg/gil/pull/320  

Use of `std::extent` was introduced in PR #200 but it turns out as not applicable for `std::array` or derived types (e.g. `kernel_1d_fixed`).  
This led to obtaining invalid size of `kernel_1d_fixed` and erroneous results of the rows and columns convolution.  
This change replaces `std::extent` with new public constant member `kernel_1d_fixed::static_size`.  
  
Since `kernel_1d_fixed` is derived from `std::array`, the alternative could be to use `std::tuple_size` specialization for `std::array`.  
  
Additionally, add static assertion to require that _kernel size must be odd to ensure validity at the center_.  
  
### References  
  
- #200 and related  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-07-03 13:25:46 UTC  
> Url: https://github.com/boostorg/gil/pull/320#issuecomment-508091925  

@miralshah365 This should fix the problem with black image as result from application of the `convolve_*` functions.

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-07-03 18:52:46 UTC  
> Url: https://github.com/boostorg/gil/pull/320#issuecomment-508215082  

My private Travis CI is green https://travis-ci.org/mloskot/gil/builds/553874031

---
