# #628 CircleCI snapshots - add CRLF [Merged]

> Username: sdarwin  
> Created at: 2022-03-01 20:36:17 UTC  
> Updated at: 2023-01-19 15:11:20 UTC  
> Merged at: 2023-01-19 15:11:20 UTC  
> Closed at: 2023-01-19 15:11:20 UTC  
> Url: https://github.com/boostorg/boost/pull/628  

In this [release-tools issue](https://github.com/boostorg/release-tools/issues/32) a user reports the  .zip and .7z packages should use Windows line endings.    
  
Before the problem appeared in 2019 the CI scripts would execute the "test_pre", "test_override" and "test_post" methods. Lately, only "test_override" runs.   "test_pre" was where line endings were set.

---
