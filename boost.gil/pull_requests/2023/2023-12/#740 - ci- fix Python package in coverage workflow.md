# #740 ci: fix Python package in coverage workflow [Merged]

> Username: striezel  
> Created at: 2023-12-25 22:22:40 UTC  
> Updated at: 2025-08-03 18:11:23 UTC  
> Merged at: 2023-12-25 22:59:52 UTC  
> Closed at: 2023-12-25 22:59:52 UTC  
> Url: https://github.com/boostorg/gil/pull/740  

### Description  
  
This pull request fixes the `E: Package 'python' has no installation candidate` error in the coverage workflow by providing an existing package.  
  
The Python package in Ubuntu 22.04 is `python3`, but I decided to go for [`python-is-python3`](https://packages.ubuntu.com/jammy/python-is-python3) instead, because that makes sure that `python3` is installed (as dependency) and also creates a symlink from `/usr/bin/python` to `python3` as a way to make sure that commands with just `python` (instead of `python3`) also invoke the installed version of Python 3.  
  
### References  
  
See https://github.com/boostorg/gil/actions/runs/7317309558/job/19932692704 for an example of a currently failing coverage job.  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: codecov[bot]  
> Created_at: 2023-12-25 22:32:42 UTC  
> Url: https://github.com/boostorg/gil/pull/740#issuecomment-1869138107  

## [Codecov](https://app.codecov.io/gh/boostorg/gil/pull/740?src=pr&el=h1&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) Report  
All modified and coverable lines are covered by tests :white_check_mark:  
> Comparison is base [(`1df8c24`)](https://app.codecov.io/gh/boostorg/gil/commit/1df8c2407e71dd80d258b573d67925273e555810?el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 81.10% compared to head [(`8888bf5`)](https://app.codecov.io/gh/boostorg/gil/pull/740?src=pr&el=desc&utm_medium=referral&utm_source=github&utm_content=comment&utm_campaign=pr+comments&utm_term=boostorg) 82.12%.  
> Report is 7 commits behind head on develop.  
  
<details><summary>Additional details and impacted files</summary>  
  
  
```diff  
@@             Coverage Diff             @@  
##           develop     #740      +/-   ##  
===========================================  
+ Coverage    81.10%   82.12%   +1.02%       
===========================================  
  Files          117      117                
  Lines         5171     5355     +184       
===========================================  
+ Hits          4194     4398     +204       
+ Misses         977      957      -20       
```  
  
</details>

---

## Review 2 [Commented]

> Username: mloskot  
> Created_at: 2023-12-25 22:40:33 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/740#pullrequestreview-1795960015  

Thank you!

---
