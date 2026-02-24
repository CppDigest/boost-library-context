# #5 Port cmake scripts to linux [Closed]

> Username: seelabs  
> Created at: 2016-04-28 13:43:30 UTC  
> Updated at: 2016-05-10 15:56:47 UTC  
> Closed at: 2016-05-09 15:34:49 UTC  
> Url: https://github.com/boostorg/beast/pull/5  

Modified root CMakeLists.txt to find the boost libraries. Every executable will be linked against the boost libs. I also downgraded the required CMake to 3.2 (that's what's on my system).  
  
CXX_FLAGS should be the same as those in Jamroot.

---

## Comment 1

> Username: seelabs  
> Created_at: 2016-04-29 15:19:55 UTC  
> Url: https://github.com/boostorg/beast/pull/5#issuecomment-215752277  

Rebased against master. I had to make some changes to support the examples directory. In particular:  
  
1) I had to use `target_link_libraries` for each executable instead of one command in the root CMakeLists.txt  
  
2) Each executable links in the superset of boost libraries needed by all the exes, even if it doesn't need it (filesystem, for example).  
  
I'd like to resolve these issues in a future PR

---

## Comment 2

> Username: vinniefalco  
> Created_at: 2016-05-09 15:34:49 UTC  
> Url: https://github.com/boostorg/beast/pull/5#issuecomment-217900096  

Merged

---
