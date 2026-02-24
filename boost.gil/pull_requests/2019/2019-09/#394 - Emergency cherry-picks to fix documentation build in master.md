# #394 [merge to master] Emergency cherry-picks to fix documentation build in master [Merged]

> Username: mloskot  
> Created at: 2019-09-26 09:16:28 UTC  
> Updated at: 2019-10-23 21:42:57 UTC  
> Merged at: 2019-09-30 17:33:32 UTC  
> Closed at: 2019-09-30 17:33:32 UTC  
> Url: https://github.com/boostorg/gil/pull/394  

**WARNING: This PR targets the MASTER branch**  
  
<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
This is a _minimal fix_ cherry-picking of two essentuak commits from the **develop** branch into **master** branch in order to fix the documentation building issue leading to missing GIL docs from 1.70 and 1.71 releases:  
  
- 4ed7701 (excluding `test/core/color_base` present in **develop** only)  
- 628bfc2  
  
See https://github.com/boostorg/gil/issues/351#issuecomment-535383358 and earlier comments there for detailed discussion.  
  
Additionally, two auxiliary commits have been cherry-picked here, 99f813846ab5a2b68ae108d0d5275aa7bf862f5a and d0cb17adefb53c4c7972ba897353cecf5c5ec1d6, in order to update Travis CI configuration and keep our Travis CI builds green for master.  
  
### References  
  
- Fixes #351  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass   
   - AppVeyor and Travis CI are required status checks  
   - We can accept the two AzP failures (see https://github.com/boostorg/gil/pull/394#issuecomment-535448127)  
   - Pass https://travis-ci.org/boostorg/gil/builds/590035618  
   - Pass https://ci.appveyor.com/project/stefanseefeld/gil/builds/27684619  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2019-09-26 10:47:38 UTC  
> Updated_at: 2019-09-27 12:37:30 UTC  
> Url: https://github.com/boostorg/gil/pull/394#issuecomment-535448127  

## Explanation for two job failing on Azure Pipeline  
  
https://dev.azure.com/boostorg/gil/_build/results?buildId=683&view=logs  
  
The AzP builds on Ubuntu are failing due outdated configuration and infrastructure with clashing Boost versions (old deployed and old system-wide).  See details in PR #277.  
Since we are not yet ready for full merge of develop to master and we don't want to cherry-pick too many commits here, we need to accept these two failing build jobs on AzP.  
  
## Conclusion  
  
This **branch is ready** to be **truly merged** (no squashing please!).

---

## Comment 2

> Username: mloskot  
> Created_at: 2019-09-26 16:50:33 UTC  
> Updated_at: 2019-09-26 19:27:29 UTC  
> Url: https://github.com/boostorg/gil/pull/394#issuecomment-535591116  

@stefanseefeld This is PR ready to review.  
This needs to be done ASAP, so if now objection arrives in a day or two, I will merge it as is.

---

## Comment 3

> Username: mloskot  
> Created_at: 2019-09-27 21:01:58 UTC  
> Url: https://github.com/boostorg/gil/pull/394#issuecomment-536095394  

@stefanseefeld & @boostorg/gil-developers This is a heads up that I'm going to merge this soon

---

## Comment 4

> Username: mloskot  
> Created_at: 2019-09-30 17:32:01 UTC  
> Updated_at: 2019-09-30 17:32:39 UTC  
> Url: https://github.com/boostorg/gil/pull/394#issuecomment-536666326  

I'm about to perform an intentional [true merge](https://git-scm.com/docs/git-merge#_true_merge) of this PR into `master` branch, so this change sticks out in the history and can be inspected easily.

---
