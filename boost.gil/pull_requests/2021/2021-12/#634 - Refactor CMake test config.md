# #634 Refactor CMake test config [Merged]

> Username: lpranam  
> Created at: 2021-12-30 04:54:56 UTC  
> Updated at: 2022-02-09 14:39:03 UTC  
> Merged at: 2022-02-09 14:17:47 UTC  
> Closed at: 2022-02-09 14:17:47 UTC  
> Url: https://github.com/boostorg/gil/pull/634  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
I don't think we need multiple loops here unless I am missing something? @simmplecoder   
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [ ] Add test case(s)  
- [ ] Ensure all CI builds pass  
- [ ] Review and approve

---

## Review 1 [Approved]

> Username: simmplecoder  
> Created_at: 2021-12-30 06:30:37 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/634#pullrequestreview-841722675  

Yes, there is no need for two loops. I believe I did it to just separate my work from Miral's, but there is no need for that anymore.

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2022-01-03 16:36:06 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/634#pullrequestreview-842875371  

LGTM

---
