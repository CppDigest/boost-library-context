# #419 Prefix CMake parameters with BOOST_ [Merged]

> Username: sourabhdharpure  
> Created at: 2020-01-15 10:29:48 UTC  
> Updated at: 2020-01-16 19:14:11 UTC  
> Merged at: 2020-01-16 19:14:10 UTC  
> Closed at: 2020-01-16 19:14:11 UTC  
> Url: https://github.com/boostorg/gil/pull/419  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Prefix CMake parameters with BOOST_  
  
### References  
This pull request closes  #412   
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2020-01-15 19:19:14 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/419#pullrequestreview-343459347  

Thank you for this PR, it's very helpful.  
  
Could you also change the `GIL_` options used in multiple places in the AzP configuration?  
  
For example:  
  
https://github.com/boostorg/gil/blob/4d39e4b5bef36af78f3a2778d856cdcce1f1fec1/.ci/azure-pipelines/steps-cmake-build-and-test.yml#L21

---

## Comment 2

> Username: sourabhdharpure  
> Created_at: 2020-01-16 13:56:03 UTC  
> Url: https://github.com/boostorg/gil/pull/419#issuecomment-575162091  

Hello @mloskot,  
I have changed the GIL_ option in the AzP configuration  
Do I  also have to change the gil_(lower case)?  
Is there are remaining file that needs to be changed?

---

## Comment 3

> Username: mloskot  
> Created_at: 2020-01-16 19:11:57 UTC  
> Url: https://github.com/boostorg/gil/pull/419#issuecomment-575300162  

@sourabhdharpure   
> Do I also have to change the gil_(lower case)?  
  
No, no need to change the `gil_` anywywere in any scripts. Thanks for noticing it and asking though.

---

## Review 4 [Approved]

> Username: mloskot  
> Created_at: 2020-01-16 19:12:57 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/419#pullrequestreview-344150558  

Looks good.  
Thank you for your contribution to GIL!

---
