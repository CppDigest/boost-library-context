# #680 Add GitHub Actions to build test//minimal with Clang, GCC, MSVC [Merged]

> Username: mloskot  
> Created at: 2020-03-09 23:23:41 UTC  
> Updated at: 2020-03-16 13:04:16 UTC  
> Merged at: 2020-03-09 23:41:27 UTC  
> Closed at: 2020-03-09 23:41:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/680  

This is to enable a minimal CI check for the pull requests, to assist  
contributors and reviewers, as well as for direct commits and merges.  
  
For each toolset, this runs permutation of `b2 cxxstd=03,11,14,17,2a variant=debug,release address-model=32,64`, with `cxxstd` list tailored according to the actual toolset.  
  
See build reports sample via  
- [Actions tab for repo](https://github.com/boostorg/geometry/actions)  
- [Checks tab for PR](https://github.com/boostorg/geometry/pull/680/checks)  
  
------  
  
The CI for pull requests was discussed in https://lists.boost.org/geometry/2020/03/3791.php

---
