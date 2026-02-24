# #1518 Android: Use compiler pre-defined __ANDROID__ [Closed]

> Username: gjasny  
> Created at: 2019-03-14 14:48:27 UTC  
> Updated at: 2019-03-18 21:36:32 UTC  
> Closed at: 2019-03-18 21:36:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1518  

The ANDROID define us usually originationg from he build system  
(like CMake, ndk-build, etc.). But some buildsystems like Bazel  
do not set it which brakes Android platform detection.  
  
Better use __ANDROID__ (like boost predef does) which is  
predefined by the compiler if targeting Android.

---

## Review 1 [Approved]

> Username: vinniefalco  
> Created_at: 2019-03-14 15:14:51 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/beast/pull/1518#pullrequestreview-214575756  

Seems reasonable, although I do not have an Android set up to test

---

## Comment 2

> Username: gjasny  
> Created_at: 2019-03-18 21:21:15 UTC  
> Url: https://github.com/boostorg/beast/pull/1518#issuecomment-474106062  

Do you know how I can re-trigger travis to get a green build? I isn't the red build a merge blocker?

---

## Comment 3

> Username: djarek  
> Created_at: 2019-03-18 21:35:16 UTC  
> Url: https://github.com/boostorg/beast/pull/1518#issuecomment-474110817  

Vinnie has his own workflow which doesn't register with github, your PR is already merged into develop:  
https://github.com/boostorg/beast/pull/1521/commits

---

## Comment 4

> Username: vinniefalco  
> Created_at: 2019-03-18 21:36:32 UTC  
> Url: https://github.com/boostorg/beast/pull/1518#issuecomment-474111218  

Yep, this got merged already - thanks!

---
