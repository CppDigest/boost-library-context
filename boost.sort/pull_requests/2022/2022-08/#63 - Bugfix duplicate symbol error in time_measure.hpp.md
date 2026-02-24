# #63 Bugfix duplicate symbol error in time_measure.hpp [Merged]

> Username: marksantaniello  
> Created at: 2022-08-16 23:25:35 UTC  
> Updated at: 2022-11-07 01:50:50 UTC  
> Merged at: 2022-11-07 01:50:49 UTC  
> Closed at: 2022-11-07 01:50:49 UTC  
> Url: https://github.com/boostorg/sort/pull/63  

Free functions that are defined in header files must be qualified inline in order to avoid duplicate symbol errors at link time.  Bugfix two cases:  
- boost::sort::common::now()  
- boost::sort::common::subtract_time()

---

## Comment 1

> Username: spreadsort  
> Created_at: 2022-08-20 16:33:31 UTC  
> Url: https://github.com/boostorg/sort/pull/63#issuecomment-1221362545  

I see a test failure:  
  
...failed clang-linux.compile.c++ bin.v2/libs/sort/test/test_parallel_stable_sort.test/clang-linux-3.8/debug/cxxstd-14-iso/optimization-speed/threading-multi/visibility-hidden/test_parallel_stable_sort.o...  
...skipped <pbin.v2/libs/sort/test/test_parallel_stable_sort.test/clang-linux-3.8/debug/cxxstd-14-iso/optimization-speed/threading-multi/visibility-hidden>test_parallel_stable_sort for lack of <pbin.v2/libs/sort/test/test_parallel_stable_sort.test/clang-linux-3.8/debug/cxxstd-14-iso/optimization-speed/threading-multi/visibility-hidden>test_parallel_stable_sort.o...  
...skipped <pbin.v2/libs/sort/test/test_parallel_stable_sort.test/clang-linux-3.8/debug/cxxstd-14-iso/optimization-speed/threading-multi/visibility-hidden>test_parallel_stable_sort.run for lack of <pbin.v2/libs/sort/test/test_parallel_stable_sort.test/clang-linux-3.8/debug/cxxstd-14-iso/optimization-speed/threading-multi/visibility-hidden>test_parallel_stable_sort...  
clang-linux.compile.c++ bin.v2/libs/sort/test/test_parallel_stable_sort.test/clang-linux-3.8/debug/cxxstd-1z-iso/optimization-speed/threading-multi/visibility-hidden/test_parallel_stable_sort.o  
In file included from libs/sort/test/test_parallel_stable_sort.cpp:20:  
In file included from ./boost/sort/parallel_stable_sort/parallel_stable_sort.hpp:23:  
./boost/sort/sample_sort/sample_sort.hpp:376:25: error: 'this' cannot be explicitly captured when the capture default is '='  
        auto func = [=, this]()  
                      ~~^~~~  
1 error generated.  
  
  "clang++" -c -x c++ -std=c++1z -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -fno-inline -Wall -g -fvisibility=hidden --target=x86_64-pc-linux  -DBOOST_ALL_NO_LIB=1 -I"." -I".."   -o "bin.v2/libs/sort/test/test_parallel_stable_sort.test/clang-linux-3.8/debug/cxxstd-1z-iso/optimization-speed/threading-multi/visibility-hidden/test_parallel_stable_sort.o" "libs/sort/test/test_parallel_stable_sort.cpp"

---

## Comment 2

> Username: marksantaniello  
> Created_at: 2022-08-20 16:42:04 UTC  
> Url: https://github.com/boostorg/sort/pull/63#issuecomment-1221363989  

Seems unrelated to this PR.  I think it was caused by this commit, which added "this" in the lambda capture list.  
https://github.com/boostorg/sort/commit/d2b2cf631f7198b2e0fdf51fd7abf478ca0eb4de

---
