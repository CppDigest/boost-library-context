# #313 Implementation of threshold_binary and threshold_truncate [Merged]

> Username: miralshah365  
> Created at: 2019-06-18 17:37:23 UTC  
> Updated at: 2020-02-22 13:11:40 UTC  
> Merged at: 2019-06-18 19:42:44 UTC  
> Closed at: 2019-06-18 19:42:44 UTC  
> Url: https://github.com/boostorg/gil/pull/313  

### Description  
  
*Two variant of Binary threshold Implemented*  
1. **Simple Binary Threshold**  
If the pixel value is more than the threshold then the pixel value is set to maximum or else to 0  
2. **Inverse Binary Threshold**  
If the pixel value is more than the threshold then the pixel value is set to 0 or else to maximum  
  
*Two Truncation threshold Implemented*  
1. **Threshold Truncate**  
If the pixel value is more than the threshold then it is truncated if the direction specified is regular. If the direction is in inverse then values below the thresholds are truncated.  
2. **Truncation to zero**  
If the pixel value is less than the threshold then it is set to zero if the direction specified is regular. If the direction is in inverse then values above the thresholds are set to zero.  
  
### References  
  
- Closes #310   
  
### Tasklist  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2019-06-18 17:40:18 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/313#pullrequestreview-251255212  

This work is ready for approval.  
  
It has been already reviewed as PR at https://github.com/BoostGSoC19/gil-miral/pull/1 and this PR is just a copy following the decision and agreement to work from Miral's private fork and submit against upstream repo.

---
