# #515 Add histogram matching algorithm [Merged]

> Username: codejaeger  
> Created at: 2020-08-24 16:30:44 UTC  
> Updated at: 2021-01-24 20:33:39 UTC  
> Merged at: 2021-01-24 20:33:39 UTC  
> Closed at: 2021-01-24 20:33:39 UTC  
> Url: https://github.com/boostorg/gil/pull/515  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Histogram matching is a generalised version of the algorithm histogram equalization.   
It aims to match the histogram obtained from one input image to another reference image, transforming the input image in the process.  
   
<!-- What does this pull request do? -->  
  
This PR adds code, tests, doc & an example to use the histogram matching feature implemented.   
  
### Dependency  
  
This PR requires support of GIL histogram class currently running in PR #499. Reviewing and merging can only be done after completion of the former PR.  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
Wiki - https://en.wikipedia.org/wiki/Histogram_matching  
Related PR - #442  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Add test case(s)  
- [x] Ensure all CI builds pass - R.I.P. Travis CI  
- [x] Review and approve

---

## Comment 1

> Username: mloskot  
> Created_at: 2021-01-24 13:14:56 UTC  
> Url: https://github.com/boostorg/gil/pull/515#issuecomment-766346389  

@codejaeger I've just `git merge`-updated your `gsoc/histogram_matching` branch from the latest `develop`

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2021-01-24 20:33:14 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/515#pullrequestreview-574966466  

@codejaeger I'm happy to merge this PR. I'm also hoping you'll be available to continue improving the histogram support in GIL. Thank you for your work.

---
