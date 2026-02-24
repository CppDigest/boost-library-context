# #625 [for_each] Fix for_each_segment to avoid incrementing iterator over empty range [Merged]

> Username: mloskot  
> Created at: 2019-10-09 19:06:50 UTC  
> Updated at: 2019-10-13 15:23:47 UTC  
> Merged at: 2019-10-13 15:23:31 UTC  
> Closed at: 2019-10-13 15:23:31 UTC  
> Url: https://github.com/boostorg/geometry/pull/625  

Added test to verifying the fix.  
Fixes #624  
  
----  
  
The added tests can reproduce the bug in the current `for_each_segment` implementation without the proposed fix:  
  
```console  
bionic:/mnt/d/boost.wsl/libs/geometry/test/algorithms$ g++  -std=gnu++2a -fvisibility-inlines-hidden -fPIC -m64 -pthread -O0 -fno-inline -Wall -g -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_NO_AUTO_PTR -I".." -I"../../../.." -I"../../../../boost/geometry/extensions/contrib/ttmath" -c -o "../../../../bin.v2/libs/geometry/test/algorithms/algorithms_for_each.test/gcc-7/debug/threading-multi/visibility-hidden/for_each.o" "for_each.cpp" && g++   -std=gnu++2a -o "../../../../bin.v2/libs/geometry/test/algorithms/algorithms_for_each.test/gcc-7/debug/threading-multi/visibility-hidden/algorithms_for_each" -Wl,--start-group "../../../../bin.v2/libs/geometry/test/algorithms/algorithms_for_each.test/gcc-7/debug/threading-multi/visibility-hidden/for_each.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -g -fvisibility=hidden -fvisibility-inlines-hidden  
  
bionic:/mnt/d/boost.wsl/libs/geometry/test/algorithms$ ../../../../bin.v2/libs/geometry/test/algorithms/algorithms_for_each.test/gcc-7/debug/threading-multi/visibility-hidden/algorithms_for_each  
Running 1 test case...  
unknown location(0): fatal error: in "test_main_caller( argc, argv )": memory access violation at address: 0x00000000: no mapping at fault address  
../algorithms/test_for_each.hpp(176): last checkpoint  
  
*** 1 failure is detected in the test module "Test Program"  
```

---

## Review 1 [Approved]

> Username: barendgehrels  
> Created_at: 2019-10-12 10:43:10 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/geometry/pull/625#pullrequestreview-300999624  

📁 include/boost/geometry/algorithms/for_each.hpp

```diff
 111 |         iterator_type it = boost::begin(range);
 112 |+         if (it == boost::end(range))
 113 |+             return;
```

> Username: barendgehrels  
> Created_at: 2019-10-12 10:43:04 UTC  
> Updated_at: 2019-10-12 18:50:27 UTC  
> Url: https://github.com/boostorg/geometry/pull/625#discussion_r334232029  

Thank you Mateusz! Can you add curly braces around `return;`?

> Username: mloskot  
> Created_at: 2019-10-12 18:50:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/625#discussion_r334249289  

Sure, done.

> Username: barendgehrels  
> Created_at: 2019-10-13 14:32:01 UTC  
> Updated_at: 2019-10-13 14:32:02 UTC  
> Url: https://github.com/boostorg/geometry/pull/625#discussion_r334280265  

Thanks


---

## Comment 2

> Username: mloskot  
> Created_at: 2019-10-13 14:36:16 UTC  
> Url: https://github.com/boostorg/geometry/pull/625#issuecomment-541423532  

Barend, Shall I squash and merge myself?

---

## Comment 3

> Username: barendgehrels  
> Created_at: 2019-10-13 15:05:28 UTC  
> Url: https://github.com/boostorg/geometry/pull/625#issuecomment-541426134  

Sure, fine!

---
