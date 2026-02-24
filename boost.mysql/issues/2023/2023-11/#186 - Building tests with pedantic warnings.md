# #186 - Building tests with pedantic warnings [Closed]

> Username: anarthal  
> Created at: 2023-11-27 23:33:33 UTC  
> Updated at: 2023-12-26 16:04:11 UTC  
> Closed at: 2023-12-26 16:04:11 UTC  
> Url: https://github.com/boostorg/mysql/issues/186  

We build with pedantic warnings in CMake but no in B2 (where we perform compiler coverage). Fix as many of these warnings as possible and suppress the others. Make CMake and B2 flags match as much as possible.  
  
This has been reported in #183, since some users build the tests using CMake as part of their own builds.
