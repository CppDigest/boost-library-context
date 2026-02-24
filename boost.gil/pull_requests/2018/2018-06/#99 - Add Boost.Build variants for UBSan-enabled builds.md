# #99 Add Boost.Build variants for UBSan-enabled builds [Merged]

> Username: mloskot  
> Created at: 2018-06-20 20:13:27 UTC  
> Updated at: 2018-06-21 21:20:05 UTC  
> Merged at: 2018-06-21 21:20:01 UTC  
> Closed at: 2018-06-21 21:20:01 UTC  
> Url: https://github.com/boostorg/gil/pull/99  

(NOTE: _Assigned to self - if accepted, I'd like to merge myself. Thanks._)  
  
### Description: what does this pull request do?  
  
The build variants enable group of checks offered by clang  
[UndefinedBehaviorSanitizer](https://clang.llvm.org/docs/UndefinedBehaviorSanitizer.html) detector.  
  
Add [sanitizers suppression](https://clang.llvm.org/docs/SanitizerSpecialCaseList.html) file in `.ci/blacklist.supp` based on copy of [the file from Boost.Beast](https://github.com/boostorg/beast/blob/develop/tools/blacklist.supp).  
  
Update Travis CI:  
  
- Add build jobs for each of the three new UBSan variants.  
- Display COMPILER and VARIANT first as these two are most important  
    details while inspecting the build matrix  
    (TOOLSET can be derived from COMPILER).  
  
### Tasklist  
  
- [x] Set **The Grand Merge** milestone (@stefanseefeld OK?)  
- [x] Review  
- [x] Adjust for comments  
- [ ] All CI builds and checks have passed  
  
-----  
  
I've built the UBSan variants and there are issues - to be checked/fixed after this is merged.

---

## Comment 1

> Username: mloskot  
> Created_at: 2018-06-20 22:42:16 UTC  
> Updated_at: 2018-06-20 22:43:22 UTC  
> Url: https://github.com/boostorg/gil/pull/99#issuecomment-398920644  

First build of this PR at https://travis-ci.org/boostorg/gil/builds/394728281 suggests that `variant=debug,release` merge can cause timeouts.  
However, it's interesting that the 1st (GCC 5) and 4th (clang 3.9) builds completed in ~20 minutes.  
I'd keep `variant=release` only really or may be have one `debug` with the latest GCC.  
  
## Question  
  
Do we really need to build both variants, `debug` and `release` for every compiler version?

---

## Comment 2

> Username: stefanseefeld  
> Created_at: 2018-06-21 00:35:40 UTC  
> Url: https://github.com/boostorg/gil/pull/99#issuecomment-398939864  

what is the nature of the timeout ? Would it help to split builds up, so "release" and "debug" would be separate builds ? (Given that we discovered certain failures only with particular compiler flags (optimization, notably), I'd very much prefer to keep the coverage. If any, we should remove the debug builds.  
And yes, I observed excessively long compilation times with certain gcc versions (interestingly, more recent versions).

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-06-21 07:05:47 UTC  
> Updated_at: 2018-06-21 07:08:32 UTC  
> Url: https://github.com/boostorg/gil/pull/99#issuecomment-398998533  

@stefanseefeld   
> what is the nature of the timeout ?   
  
Not sure I get your question, but https://docs.travis-ci.com/user/customizing-the-build#Build-Timeouts  
  
```  
When a job produces no log output for 10 minutes.  
When a job on a public repository takes longer than 50 minutes.  
When a job on a private repository takes longer than 120 minutes.  
```  
  
> Would it help to split builds up, so "release" and "debug" would be separate builds ?  
  
That is what we have been doing, and I proposed to merge the two variants into single build job.  
  
>  I'd very much prefer to keep the coverage.   
  
No problem, I have reverted the merging of build variants and the build jobs coverage is back to the original, see https://travis-ci.org/boostorg/gil/builds/394879984  
  
> And yes, I observed excessively long compilation times with certain gcc versions (interestingly, more recent versions).  
  
This is quite a curiosity. Also the latest build above shows that GCC 5 is blazing fast comparing to 6 and 7. Perhaps, with GCC 6 and 7 we generate more detailed warnings - I imagine, stdout/stderr I/O may slow things down. If I find a moment, I will look at the detailed logs and compare them.

---

## Review 4 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-06-21 12:13:48 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/99#pullrequestreview-130787050  

This looks great. Thanks for the effort !

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-06-21 20:10:10 UTC  
> Updated_at: 2018-06-21 21:19:54 UTC  
> Url: https://github.com/boostorg/gil/pull/99#issuecomment-399227252  

Thanks for the review.   
  
To summary:  
  
- Travis CI build jobs `ubsan_integer` and `ubsan_undefined` should fail due to issues detected by the sanitizers in GIL - https://travis-ci.org/boostorg/gil/builds/395130073  
- AppVeyor build jobs should pass

---
