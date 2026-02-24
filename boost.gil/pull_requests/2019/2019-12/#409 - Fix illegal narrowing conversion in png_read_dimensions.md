# #409 Fix illegal narrowing conversion in png_read_dimensions [Merged]

> Username: mloskot  
> Created at: 2019-12-13 19:40:53 UTC  
> Updated at: 2019-12-14 18:40:19 UTC  
> Merged at: 2019-12-14 18:39:59 UTC  
> Closed at: 2019-12-14 18:39:59 UTC  
> Url: https://github.com/boostorg/gil/pull/409  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
The `gil/extension/io/{format}/old.hpp` interfaces are covered by old  
tests which are part of full I/O test suite only. Due to limited CI  
resources we do not run full tests, hence this has not been caught.  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
Fixes #408  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] ~Add test case(s)~ - see comment above  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---
