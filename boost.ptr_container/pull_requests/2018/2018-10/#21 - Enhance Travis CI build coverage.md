# #21 Enhance Travis CI build coverage [Merged]

> Username: jeking3  
> Created at: 2018-10-28 01:50:50 UTC  
> Updated at: 2018-11-04 12:01:59 UTC  
> Merged at: 2018-11-04 12:01:57 UTC  
> Closed at: 2018-11-04 12:01:57 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/21  



---

## Comment 1

> Username: jeking3  
> Created_at: 2018-10-28 15:39:27 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/21#issuecomment-433716349  

Ick...  
```  
test/ptr_set.cpp: In function ‘void test_set()’:  
test/ptr_set.cpp:104: error: ‘nullptr’ was not declared in this scope  
    "g++-4.4"   -std=c++0x -fvisibility-inlines-hidden -fPIC -m64 -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_CHRONO_THREAD_DISABLED -DBOOST_SYSTEM_STATIC_LINK=1 -DBOOST_TEST_NO_AUTO_LINK=1 -DBOOST_TIMER_STATIC_LINK=1 -DNDEBUG  -I"../.." -c -o "../../bin.v2/libs/ptr_container/test/ptr_set.test/gcc-4.4/release/cxxstd-0x-iso/visibility-hidden/ptr_set.o" "test/ptr_set.cpp"  
...failed gcc.compile.c++ ../../bin.v2/libs/ptr_container/test/ptr_set.test/gcc-4.4/release/cxxstd-0x-iso/visibility-hidden/ptr_set.o...  
```

---

## Comment 2

> Username: codecov[bot]  
> Created_at: 2018-10-29 23:54:34 UTC  
> Updated_at: 2018-11-04 00:43:21 UTC  
> Url: https://github.com/boostorg/ptr_container/pull/21#issuecomment-434122917  

# [Codecov](https://codecov.io/gh/boostorg/ptr_container/pull/21?src=pr&el=h1) Report  
> Merging [#21](https://codecov.io/gh/boostorg/ptr_container/pull/21?src=pr&el=desc) into [develop](https://codecov.io/gh/boostorg/ptr_container/commit/67915a7f8610247b7bac4ca41b03702b54f4933a?src=pr&el=desc) will **increase** coverage by `0.03%`.  
> The diff coverage is `n/a`.  
  
[![Impacted file tree graph](https://codecov.io/gh/boostorg/ptr_container/pull/21/graphs/tree.svg?width=650&token=D5x8WKDJ88&height=150&src=pr)](https://codecov.io/gh/boostorg/ptr_container/pull/21?src=pr&el=tree)  
  
```diff  
@@             Coverage Diff             @@  
##           develop      #21      +/-   ##  
===========================================  
+ Coverage    61.24%   61.28%   +0.03%       
===========================================  
  Files           35       35                
  Lines         1463     1467       +4       
  Branches       545      546       +1       
===========================================  
+ Hits           896      899       +3       
  Misses          35       35                
- Partials       532      533       +1  
```  
  
  
| [Impacted Files](https://codecov.io/gh/boostorg/ptr_container/pull/21?src=pr&el=tree) | Coverage Δ | |  
|---|---|---|  
| [.../ptr\_container/detail/reversible\_ptr\_container.hpp](https://codecov.io/gh/boostorg/ptr_container/pull/21/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wdHJfY29udGFpbmVyL2RldGFpbC9yZXZlcnNpYmxlX3B0cl9jb250YWluZXIuaHBw) | `58.95% <0%> (-0.58%)` | :arrow_down: |  
| [...e/boost/ptr\_container/detail/void\_ptr\_iterator.hpp](https://codecov.io/gh/boostorg/ptr_container/pull/21/diff?src=pr&el=tree#diff-aW5jbHVkZS9ib29zdC9wdHJfY29udGFpbmVyL2RldGFpbC92b2lkX3B0cl9pdGVyYXRvci5ocHA=) | `84% <0%> (+1.39%)` | :arrow_up: |  
  
------  
  
[Continue to review full report at Codecov](https://codecov.io/gh/boostorg/ptr_container/pull/21?src=pr&el=continue).  
> **Legend** - [Click here to learn more](https://docs.codecov.io/docs/codecov-delta)  
> `Δ = absolute <relative> (impact)`, `ø = not affected`, `? = missing data`  
> Powered by [Codecov](https://codecov.io/gh/boostorg/ptr_container/pull/21?src=pr&el=footer). Last update [67915a7...b358897](https://codecov.io/gh/boostorg/ptr_container/pull/21?src=pr&el=lastupdated). Read the [comment docs](https://docs.codecov.io/docs/pull-request-comments).

---
