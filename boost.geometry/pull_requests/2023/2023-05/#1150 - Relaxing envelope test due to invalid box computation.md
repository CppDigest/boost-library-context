# #1150 [test] Relaxing envelope test due to invalid box computation [Merged]

> Username: vissarion  
> Created at: 2023-05-22 13:35:14 UTC  
> Updated at: 2023-06-16 14:02:40 UTC  
> Merged at: 2023-05-25 08:46:37 UTC  
> Closed at: 2023-05-25 08:46:37 UTC  
> Url: https://github.com/boostorg/geometry/pull/1150  

This PR fixes some failing tests in [circleCI](https://app.circleci.com/pipelines/github/boostorg/geometry/393/workflows/0f08835f-b25a-4112-b290-47348ca4a762/jobs/19548) by relaxing the test dataset. In particular, it tests against a slightly smaller box.  
  
The commit that introduced that fail was https://github.com/boostorg/geometry/commit/092ab9da349dd0dab8b610407b0275b192d0477c in particular the replacement of boost::minmax_element with std::minmax_element. Actually, the invalid box was already there before that commit but accidentally the use of boost::minmax_element had made valid() return true.  
  
Related issue https://github.com/boostorg/geometry/issues/1148

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2023-05-22 18:06:24 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1150#pullrequestreview-1437179674  

Looks good to me

---

## Review 2 [Approved]

> Username: barendgehrels  
> Created_at: 2023-05-22 18:06:25 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/1150#pullrequestreview-1437179699  

Looks good to me

---
