# #397 Hide convolve_1d, convolve_2d and kernel_2 in namespace detail [Merged]

> Username: mloskot  
> Created at: 2019-10-23 21:41:10 UTC  
> Updated at: 2019-10-24 10:51:02 UTC  
> Merged at: 2019-10-24 10:50:56 UTC  
> Closed at: 2019-10-24 10:50:56 UTC  
> Url: https://github.com/boostorg/gil/pull/397  

## Description  
  
Following @lpranam comments on planned changes to `convolve_2d`, which will not be ready for Boost 1.72, @mloskot proposed to hide the function as implementation detail, https://lists.boost.org/boost-gil/2019/10/0316.php, until it is ready and @stefanseefeld agreed on the proposal, https://lists.boost.org/boost-gil/2019/10/0320.php.  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
- PR #347  
- PR #367  
- Issue #381 with planned changes by @lpranam too, https://lists.boost.org/boost-gil/2019/10/0315.php  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass (see https://github.com/boostorg/gil/pull/397#issuecomment-545861855)

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-10-24 10:49:37 UTC  
> Url: https://github.com/boostorg/gil/pull/397#issuecomment-545861855  

All CI-s passed but https://travis-ci.org/boostorg/gil/builds/602209281 is still pending and it may take long hours to complete.  
Since equivalent completed and passed on my private account https://travis-ci.org/mloskot/gil/builds/602209246, I'm going to merge this PR.

---
