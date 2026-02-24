# #531 - Boost.Math no longer builds on C++03; needs [ requires ] in Jamfile [Closed]

> Username: pdimov  
> Created at: 2021-02-12 12:37:29 UTC  
> Updated at: 2021-02-14 14:34:34 UTC  
> Closed at: 2021-02-14 14:34:34 UTC  
> Url: https://github.com/boostorg/math/issues/531  

Building Boost under C++03 now fails, because Math no longer builds. This is probably intentional, in which case the appropriate [ requires ] checks need to be added to build/Jamfile to disable the library build under C++03 compilers.  
  
E.g. https://travis-ci.org/github/boostorg/boost_install/jobs/758418721
