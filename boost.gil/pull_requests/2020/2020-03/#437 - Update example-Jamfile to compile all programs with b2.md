# #437 Update example/Jamfile to compile all programs with b2 [Merged]

> Username: siddhant2001  
> Created at: 2020-03-01 04:41:51 UTC  
> Updated at: 2020-03-11 05:43:34 UTC  
> Merged at: 2020-03-01 20:18:00 UTC  
> Closed at: 2020-03-01 20:18:00 UTC  
> Url: https://github.com/boostorg/gil/pull/437  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Edit the Jamfile in /example to ensure all examples build without errors.  
<!-- What does this pull request do? -->  
  
### References  
https://github.com/boostorg/gil/issues/436  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Edit Jamfile to compile all examples  
- [x] Verify all examples compile  
- [x] Verify all examples build/link (https://github.com/boostorg/gil/pull/437#issuecomment-593135692, @mloskot tried locally)  
- [x] Review and approve

---

## Review 1 [Changes requested]

> Username: mloskot  
> Created_at: 2020-03-01 19:17:59 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/gil/pull/437#pullrequestreview-366879828  

FYI, we don't build examples during the CI builds, only tests:  
  
https://github.com/boostorg/gil/blob/f3d38624f28e6b02e12a47c5f4a32c623d3051b2/Jamfile#L10  
  
Have you tried to build them locally?  
Did you use `b2 libs/gil/example` or did you use CMake with `-DBOOST_GIL_BUILD_EXAMPLES=ON` as per  
  
https://github.com/boostorg/gil/blob/f3d38624f28e6b02e12a47c5f4a32c623d3051b2/CMakeLists.txt#L244-L246  
  
Please, update the PR description about if/what have you tried to build.

---

## Comment 2

> Username: siddhant2001  
> Created_at: 2020-03-01 19:32:36 UTC  
> Url: https://github.com/boostorg/gil/pull/437#issuecomment-593135692  

> Did you use `b2 libs/gil/example` or did you use CMake with `-DBOOST_GIL_BUILD_EXAMPLES=ON` as per  
>   
> https://github.com/boostorg/gil/blob/f3d38624f28e6b02e12a47c5f4a32c623d3051b2/CMakeLists.txt#L244-L246  
  
  
I used `b2 libs/gil/example`

---

## Review 3 [Approved]

> Username: mloskot  
> Created_at: 2020-03-01 20:16:41 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/437#pullrequestreview-366883371  

LGTM  
  
NOTE: We still run `compile` for the examples, to verify their syntax. There is no linking involved yet, so no executables are produced.

---

## Comment 4

> Username: mloskot  
> Created_at: 2020-03-01 20:18:20 UTC  
> Url: https://github.com/boostorg/gil/pull/437#issuecomment-593140210  

@siddhant2001 Thanks

---

## Comment 5

> Username: himanshusainie  
> Created_at: 2020-03-11 05:04:12 UTC  
> Url: https://github.com/boostorg/gil/pull/437#issuecomment-597445882  

is this issue open or is it solved

---

## Comment 6

> Username: lpranam  
> Created_at: 2020-03-11 05:30:43 UTC  
> Url: https://github.com/boostorg/gil/pull/437#issuecomment-597451506  

@himanshusainie as you can see it is already merged :man_facepalming:

---

## Comment 7

> Username: himanshusainie  
> Created_at: 2020-03-11 05:43:34 UTC  
> Url: https://github.com/boostorg/gil/pull/437#issuecomment-597454297  

Ok

---
