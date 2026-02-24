# #35 Minor fixes [Merged]

> Username: Flast  
> Created at: 2014-11-10 09:02:02 UTC  
> Updated at: 2014-11-11 23:39:17 UTC  
> Merged at: 2014-11-11 23:39:17 UTC  
> Closed at: 2014-11-11 23:39:17 UTC  
> Url: https://github.com/boostorg/fusion/pull/35  

- [#3225](https://svn.boost.org/trac/boost/ticket/3225) already can be closed, however there are no tests about that.  
  - 95d2a11 Add tests for [#3225](https://svn.boost.org/trac/boost/ticket/3225)  
- Metafuncsions and documents: `copy` / `move`  
  - 916df63 Add result_of::{copy,move}, close [#5886](https://svn.boost.org/trac/boost/ticket/5886)  
- SFINAE-friendly result_of  
  - 465c3f2 result_of::{copy,move,swap} are now SFINAE-friendly  
  
Tested under x86_64-linux-gnu with GCC 4.8.3/Clang 3.4 (both of C++98 / C++11 mode).

---
