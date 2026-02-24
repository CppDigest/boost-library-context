# #747 feat: add tell() and error() functions to istream + ostream devices [Merged]

> Username: striezel  
> Created at: 2024-05-17 01:26:12 UTC  
> Updated at: 2024-05-17 14:30:08 UTC  
> Merged at: 2024-05-17 09:43:44 UTC  
> Closed at: 2024-05-17 09:43:44 UTC  
> Url: https://github.com/boostorg/gil/pull/747  

### Description  
  
Adds `tell()` and `error()` functions to `istream_device` and `ostream_device` classes.  
  
The class `file_stream_device` already has such functions, and now both `istream_device` and `ostream_device` have them, too, making the device classes more consistent.  
  
Closes #724.  
  
### References  
  
See #724.  
  
### Tasklist  
  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2024-05-17 01:38:10 UTC  
> Url: https://github.com/boostorg/gil/pull/747#issuecomment-2116473388  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/747?dropdown=coverage&src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Project coverage is 82.12%. Comparing base [(`322c4e2`)](https://app.codecov.io/gh/boostorg/gil/commit/322c4e2e191458383db0f2873dd3301f05a7d137?dropdown=coverage&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) to head [(`e602fc2`)](https://app.codecov.io/gh/boostorg/gil/pull/747?dropdown=coverage&src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg).  
> Report is 3 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@           Coverage Diff            @@  
##           develop     #747   +/-   ##  
========================================  
  Coverage    82.12%   82.12%             
========================================  
  Files          117      117             
  Lines         5355     5355             
========================================  
  Hits          4398     4398             
  Misses         957      957             
```  
  
</details>

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2024-05-17 09:43:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/747#pullrequestreview-2062828829  

Awesome, thanks a lot!

---
