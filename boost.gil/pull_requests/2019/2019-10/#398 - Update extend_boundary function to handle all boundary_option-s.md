# #398 Update extend_boundary function to handle all boundary_option-s [Merged]

> Username: lpranam  
> Created at: 2019-10-25 21:23:40 UTC  
> Updated at: 2019-10-28 16:19:29 UTC  
> Merged at: 2019-10-28 16:19:28 UTC  
> Closed at: 2019-10-28 16:19:28 UTC  
> Url: https://github.com/boostorg/gil/pull/398  

### Description  
Added new case where `extend_padded` boundary option will assume source view to be already padded and will return the original image.   
Other boundary options `output_ignore` and `output_zero` will not be allowed in this function as they do not make any sense with boundary extension but to be used with convolution operations.  
  
### References  
closes #391   
  
### Tasklist  
- [X] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2019-10-25 21:57:12 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/398#pullrequestreview-307470534  

LGTM. Thanks!

---
