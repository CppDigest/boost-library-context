# #315 Add Windows ARM64 support [Merged]

> Username: vask2108  
> Created at: 2025-09-08 22:00:59 UTC  
> Updated at: 2025-09-24 06:23:39 UTC  
> Merged at: 2025-09-24 06:23:34 UTC  
> Closed at: 2025-09-24 06:23:34 UTC  
> Url: https://github.com/boostorg/context/pull/315  

Fixes issues :  
https://github.com/boostorg/boost/issues/1070  
https://github.com/boostorg/context/issues/296  
Related to PR: https://github.com/boostorg/boost/pull/1077

---

## Comment 1

> Username: vask2108  
> Created_at: 2025-09-09 16:14:11 UTC  
> Url: https://github.com/boostorg/context/pull/315#issuecomment-3271401353  

@olk   
  
Can you please review this PR.  
  
Thank you.

---

## Comment 2

> Username: vask2108  
> Created_at: 2025-09-10 11:55:36 UTC  
> Url: https://github.com/boostorg/context/pull/315#issuecomment-3274621373  

@olk   
This re-using existing asm files for windows arm64 appears help only to build not during testing. I am working on alternate solution. I will update this PR soon.   
  
Thank you.

---

## Comment 3

> Username: vask2108  
> Created_at: 2025-09-11 15:09:10 UTC  
> Url: https://github.com/boostorg/context/pull/315#issuecomment-3281316352  

@olk   
This PR is ready for review now and my latest changes are here this commit   
https://github.com/boostorg/context/pull/315/commits/4aa65eea6cc2f4736565b22ecf57e3b72e30ad2d  
  
Thank you.

---

## Comment 4

> Username: Greenie0701  
> Created_at: 2025-09-19 15:51:13 UTC  
> Url: https://github.com/boostorg/context/pull/315#issuecomment-3312722695  

@vask2108 I have few doubts with respect to this PR. I know that boost context library fails to compile on Windows ARM64 even we are using winfiber implementation without disabling coroutine. IIRC, they were related missing symbol errors 'fcontext' export symbols. Where you able fix that ?

---

## Comment 5

> Username: vask2108  
> Created_at: 2025-09-19 15:59:57 UTC  
> Updated_at: 2025-09-19 16:11:49 UTC  
> Url: https://github.com/boostorg/context/pull/315#issuecomment-3312755053  

@Greenie0701   
No. I did get any coroutine related build failure. If you see the main PR https://github.com/boostorg/boost/pull/1077, It has got build passing.  I am happy to looking any issue, if there is one. Thank you.

---

## Comment 6

> Username: Greenie0701  
> Created_at: 2025-09-19 16:19:52 UTC  
> Updated_at: 2025-09-19 16:21:22 UTC  
> Url: https://github.com/boostorg/context/pull/315#issuecomment-3312830609  

I think the build failures only occur when building Context as a standalone library, not when it’s built as a Boost subpackage. This seems to be a separate issue specific to Windows ARM64 and shouldn’t block this PR from being merged.

---

## Comment 7

> Username: vask2108  
> Created_at: 2025-09-19 16:23:24 UTC  
> Url: https://github.com/boostorg/context/pull/315#issuecomment-3312843399  

@Greenie0701   
Thank you. I understand @olk is the maintainer and waiting for the review to take place  :-)

---

## Comment 8

> Username: olk  
> Created_at: 2025-09-23 16:22:00 UTC  
> Updated_at: 2025-09-23 16:22:10 UTC  
> Url: https://github.com/boostorg/context/pull/315#issuecomment-3324724579  

Did you run the unit tests?

---

## Comment 9

> Username: vask2108  
> Created_at: 2025-09-23 16:41:25 UTC  
> Url: https://github.com/boostorg/context/pull/315#issuecomment-3324798325  

@olk   
 I assume you are taking about b2 -j3 libs/context/test?  
Yes. There are passing in the CI and also my local device .  
If you enable work flows in this PR. I am hoping there should pass. If any issues, I am happy to fix   
  
Thank you.

---

## Comment 10

> Username: vask2108  
> Created_at: 2025-09-23 19:46:01 UTC  
> Url: https://github.com/boostorg/context/pull/315#issuecomment-3325321914  

@olk   
I have rebased with latest merge. If you are happy, please enable the workflows to test.  
  
Thank you.

---

## Comment 11

> Username: olk  
> Created_at: 2025-09-24 06:23:38 UTC  
> Url: https://github.com/boostorg/context/pull/315#issuecomment-3326716311  

ty

---
