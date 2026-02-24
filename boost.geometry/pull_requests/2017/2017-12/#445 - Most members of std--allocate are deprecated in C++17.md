# #445 Most members of std::allocate are deprecated in C++17 [Merged]

> Username: DanielaE  
> Created at: 2017-12-30 10:35:01 UTC  
> Updated at: 2018-01-14 07:02:56 UTC  
> Merged at: 2018-01-04 11:44:51 UTC  
> Closed at: 2018-01-04 11:44:51 UTC  
> Url: https://github.com/boostorg/geometry/pull/445  

Replace them by their cousins from std::allocator_traits.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Comment 1

> Username: awulkiew  
> Created_at: 2017-12-31 13:41:29 UTC  
> Url: https://github.com/boostorg/geometry/pull/445#issuecomment-354604434  

Thanks!  
  
However there are 2 issues:  
1. The R-tree already partially use `boost::container::allocator_traits<>` so this should be used consistently instead of `std::allocator_traits<>`. The reason is that R-tree just like Boost.Container containers emulates C++11 fetures in C++03.  
2. The changes that were done in `node/variant_static.hpp` should also be done in `node/variant_dynamic.hpp`  
  
Would you like to change the PR and propose modified version or would you prefer me to do it?

---

## Comment 2

> Username: DanielaE  
> Created_at: 2017-12-31 13:48:18 UTC  
> Url: https://github.com/boostorg/geometry/pull/445#issuecomment-354604764  

Well, ok - I will bite the bullet and rework the PR. My biggest grief is the enormous time it takes to run the test-suite after each change for both C++03 and C++17. This literally takes hours 😢

---

## Comment 3

> Username: awulkiew  
> Created_at: 2017-12-31 14:19:37 UTC  
> Updated_at: 2017-12-31 14:20:14 UTC  
> Url: https://github.com/boostorg/geometry/pull/445#issuecomment-354606277  

Thanks. I think in this case it is sufficient to run only tests relevant for the R-tree, to confirm it compiles properly. E.g. from `BOOST_ROOT/libs/geometry` you can run `../../b2 index/test/rtree` or even `../../b2 index/test/rtree/generated`. Furthremore you can run multiple tests at once by passing `-jN` parameter to `b2`. E.g. to run 4 R-tree tests at once: `../../b2 -j4 index/test/rtree/generated`. This should take a fraction of time compared to running all of the tests one by one if you have a machine suitable for it.

---

## Comment 4

> Username: DanielaE  
> Created_at: 2018-01-03 12:09:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/445#issuecomment-354997596  

Hi Adam, Happy New Year!  
  
I've reworked the patch accordingly. I hope you like it more, now!  
  
I'm still puzzled how the other two source files could slip through my fingers. It must have been my "test speed optimization" which didn't work as expected. Therefore I missed `boost::container::allocator_traits<>`, too. I've never heard about that before. Anyway, your tip with testing just the affected parts is just great - thanks for that! This running-tests-in-parallel thing is old news to me: I run all tests on 6 (out of my 8) cpu cores dedicated to testing.

---

## Comment 5

> Username: awulkiew  
> Created_at: 2018-01-04 11:44:23 UTC  
> Url: https://github.com/boostorg/geometry/pull/445#issuecomment-355262498  

Hi Daniela, Happy New Year!  
  
Thanks for the patch.

---
