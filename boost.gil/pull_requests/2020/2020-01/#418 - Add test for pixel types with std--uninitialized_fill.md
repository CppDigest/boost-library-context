# #418 Add test for pixel types with std::uninitialized_fill [Merged]

> Username: mloskot  
> Created at: 2020-01-13 22:54:59 UTC  
> Updated at: 2020-01-18 10:03:23 UTC  
> Merged at: 2020-01-18 10:03:18 UTC  
> Closed at: 2020-01-18 10:03:18 UTC  
> Url: https://github.com/boostorg/gil/pull/418  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
<!-- What does this pull request do? -->  
  
Since `std::uninitialized_fill` performs a placement new on each element in the range, it is important to ensure core and packed pixel types behave correctly.  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass

---
