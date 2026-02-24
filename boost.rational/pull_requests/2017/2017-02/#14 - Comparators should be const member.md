# #14 Comparators should be const member. [Merged]

> Username: Flast  
> Created at: 2017-02-03 08:05:47 UTC  
> Updated at: 2017-03-26 07:15:17 UTC  
> Merged at: 2017-02-08 22:19:29 UTC  
> Closed at: 2017-02-08 22:19:29 UTC  
> Url: https://github.com/boostorg/rational/pull/14  

Additionally, c++11 non-static constexpr is implicitly const member, but after c++14 it isn't. Some compilers with c++11 mode warn about that (see [rational / clang-linux-3.8~gnu++11](http://www.boost.org/development/tests/develop/output/Flast-FreeBSD11-rational-clang-linux-3-8~gnu++11-warnings.html)).  
  
Tested on both of GCC 5.4.0 and Clang 3.8.1 with gnu++98, gnu++11, and gnu++14 mode.

---

## Comment 1

> Username: DanielaE  
> Created_at: 2017-03-26 07:15:17 UTC  
> Url: https://github.com/boostorg/rational/pull/14#issuecomment-289263197  

Thanks a lot @Flast !  
These changes fix an compilation error in the Boost.Test test-suite (seen with later versions of msvc)

---
