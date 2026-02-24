# #328 Add suppressive parentheses for function-like macro [Merged]

> Username: T-Deuty  
> Created at: 2019-07-08 23:33:56 UTC  
> Updated at: 2019-07-09 08:01:20 UTC  
> Merged at: 2019-07-09 08:01:02 UTC  
> Closed at: 2019-07-09 08:01:02 UTC  
> Url: https://github.com/boostorg/gil/pull/328  

### Description  
  
This PR adds suppressing parentheses to the std::numeric_limits<UnsignedIntegralChannel>::max to avoid windows.h redefinition issues that may arise when boost/gil is used in large projects with other libraries.  
  
### Environment  
  
All relevant information like:  
  
- Compiler version: Boost.Build 4.0-git  
- Build settings:  
- GIL Version (Git ref or `<boost/version.hpp>`): 107100  
  
### References  
  
*Link related issues, pull requests here, mailing list, stackoverflow, etc.*  
  
### Tasklist  
  
- [x] Ensure all CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: T-Deuty  
> Created_at: 2019-07-09 03:41:44 UTC  
> Url: https://github.com/boostorg/gil/pull/328#issuecomment-509474833  

@mloskot please review :)  
This passes the build checks as you can see, and resolves the issue I was having when using GIL in my project. Thanks!

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2019-07-09 07:43:58 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/328#pullrequestreview-259320439  

LGTM, thanks!

---
