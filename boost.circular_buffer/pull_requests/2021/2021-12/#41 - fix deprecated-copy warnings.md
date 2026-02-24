# #41 fix deprecated-copy warnings [Merged]

> Username: apolukhin  
> Created at: 2021-12-11 09:54:45 UTC  
> Updated at: 2021-12-11 13:59:12 UTC  
> Merged at: 2021-12-11 13:59:12 UTC  
> Closed at: 2021-12-11 13:59:12 UTC  
> Url: https://github.com/boostorg/circular_buffer/pull/41  

This PR supersedes https://github.com/boostorg/circular_buffer/pull/30 , does less invasive changes.  
  
Tested with `./b2 libs/circular_buffer/test/ cxxstd=2a,03,11,,17,14 "cxxflags=-Werror=deprecated-copy" toolset=gcc-9 -j4` and `./b2 libs/circular_buffer/test/ cxxstd=2a,03,11,,17,14 "cxxflags=-Werror=deprecated-copy" toolset=clang-13 -j4`. All tests pass with the proposed patch

---
