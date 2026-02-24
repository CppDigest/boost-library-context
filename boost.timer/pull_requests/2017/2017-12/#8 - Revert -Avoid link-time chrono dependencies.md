# #8 Revert "Avoid link-time chrono dependencies" [Merged]

> Username: danieljames  
> Created at: 2017-12-13 23:48:39 UTC  
> Updated at: 2017-12-31 15:10:10 UTC  
> Merged at: 2017-12-18 18:54:30 UTC  
> Closed at: 2017-12-18 18:54:30 UTC  
> Url: https://github.com/boostorg/timer/pull/8  

This reverts commit 98954984a4b55d01380bd8f18c884dc8519133ea, from PR #5.  
  
It's causing a link error for Visual C++. This branch will be used for 1.66.0, but if it's fixed some other way for the next release then it doesn't need to be accepted.

---

## Comment 1

> Username: pdimov  
> Created_at: 2017-12-14 15:26:45 UTC  
> Url: https://github.com/boostorg/timer/pull/8#issuecomment-351742957  

I added a test for the link errors, https://github.com/boostorg/timer/commit/05ae7c47e99038c5f777c9682980d6d7f5d2b768, which currently fails under MSVC. This PR should fix these failures.

---

## Comment 2

> Username: pdimov  
> Created_at: 2017-12-14 15:56:48 UTC  
> Url: https://github.com/boostorg/timer/pull/8#issuecomment-351752140  

Correction, https://github.com/boostorg/timer/commit/e5759a6c4792060065f5e3e1a64c86531fd262ee.

---
