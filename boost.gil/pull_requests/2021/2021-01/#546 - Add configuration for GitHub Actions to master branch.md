# #546 Add configuration for GitHub Actions to master branch [Merged]

> Username: mloskot  
> Created at: 2021-01-25 20:59:01 UTC  
> Updated at: 2021-01-26 00:02:00 UTC  
> Merged at: 2021-01-25 22:53:25 UTC  
> Closed at: 2021-01-25 22:53:25 UTC  
> Url: https://github.com/boostorg/gil/pull/546  

<!-- Pull Requests MUST come from topic branch based on develop, and NEVER on `master) --->  
  
### Description  
  
Add basic GitHub Actions configuration based on mp11  
Remove Actions jobs using GCC 4.7 and 4.8 - unsupported compilers  
Run b2 with --abbreviate-paths on Windows  
The -std=c++1z is broken for clang-4.0 but no need to test it  
Add -mbig-obj to GCC on Windows  
  - That is to avoid string table overflow and file too big  
Define _GLIBCXX_USE_CXX11_ABI=0 for clang 3.5, 3.6, 3.7  
  - Should help avoid linker error:  
    `undefined reference to std::ios_base::failure::failure(char const*, std::error_code const&)`  
Disable certain check in algorithm_channel_relation test for clang<3.8  
  
(cherry picked from develop commit 81b4dc08bd9177bf9361194a66b0e70cfd6438e8)  
  
  
<!-- What does this pull request do? -->  
  
### References  
  
<!-- Any links related to this PR: issues, other PRs, mailing list threads, StackOverflow questions, etc. -->  
  
- Cherry-picked from #544   
  
### Tasklist  
  
<!-- Add YOUR OWN TASK(s), especially if your PR is a work in progress -->  
  
- [x] Ensure all CI builds pass -  RIP Travis CI, AppVeyor can be ignored.

---
