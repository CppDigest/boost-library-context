# #94 Use promote_integral in channel_invert algorithm [Merged]

> Username: mloskot  
> Created at: 2018-06-17 22:44:40 UTC  
> Updated at: 2018-06-18 17:14:19 UTC  
> Merged at: 2018-06-18 15:02:33 UTC  
> Closed at: 2018-06-18 15:02:33 UTC  
> Url: https://github.com/boostorg/gil/pull/94  

### Description  
  
This should help to avoid UB due to possible signed integer overflows, for minimum/maximum of input channel domain.  
  
### References (eg. other issues, pull requests)  
  
Fixes #89  
  
I have verified locally that this patch fixes the `channel_invert` algorithm test failure when building with clang 5.0 and `variant=release`, which originally has been failing on https://circleci.com/gh/boostorg/gil/966  Once this PR is merged, the clang 5.0 build job on CircleCI should have been fixed as well.  
  
```  
====== BEGIN OUTPUT ======  
Running 30 test cases...  
/mnt/d/boost.wsl/libs/gil/test/channel/algorithm_channel_invert.cpp(22):   
  error: in "channel_value<int>":  
    check gil::channel_invert(f.min_v_) == f.max_v_ has failed [-1 != 2147483647]  
/mnt/d/boost.wsl/libs/gil/test/channel/algorithm_channel_invert.cpp(22):  
  error: in "channel_reference<int>":  
    check gil::channel_invert(f.min_v_) == f.max_v_ has failed [-1 != 2147483647]  
/mnt/d/boost.wsl/libs/gil/test/channel/algorithm_channel_invert.cpp(22):  
  error: in "channel_reference_const<int>":  
    check gil::channel_invert(f.min_v_) == f.max_v_ has failed [-1 != 2147483647]  
  
*** 3 failures are detected in the test module "test_algorithm_channel_invert"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
```  
  
### Tasklist  
  
- [x] Review  
- [x] Adjust for comments  
- [x] All CI builds and checks have passed

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2018-06-18 15:02:49 UTC  
> Url: https://github.com/boostorg/gil/pull/94#issuecomment-398085467  

(sorry for the noise, clicked the wrong button...)

---

## Review 2 [Commented]

> Username: chhenning  
> Created_at: 2018-06-18 17:14:19 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/gil/pull/94#pullrequestreview-129656633  

Looks good.

---
