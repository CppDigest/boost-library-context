# #295 Disable free operator* of point<T> for non-arithmetic operand [Merged]

> Username: mloskot  
> Created at: 2019-04-25 19:48:30 UTC  
> Updated at: 2019-05-02 12:40:30 UTC  
> Merged at: 2019-04-29 18:59:52 UTC  
> Closed at: 2019-04-29 18:59:52 UTC  
> Url: https://github.com/boostorg/gil/pull/295  

Add Boost.Build-only test to verify compilation failure.  
  
### References  
  
Fixes #289 as better solution than #294 workaround.  
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve  
  
-----  
  
This follows suggestion from @stefanseefeld in https://github.com/boostorg/gil/pull/294#issuecomment-486805343

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-05-02 12:40:29 UTC  
> Url: https://github.com/boostorg/gil/pull/295#issuecomment-488656712  

Refined by SFINAE-friendly version of `std::common_type` in #298

---
