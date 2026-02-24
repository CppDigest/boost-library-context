# #2 [CMake] Encapsulate the disassembly generation in a reusable CMake module [Merged]

> Username: ldionne  
> Created at: 2016-12-18 21:13:31 UTC  
> Updated at: 2018-06-11 17:16:17 UTC  
> Merged at: 2016-12-18 22:17:38 UTC  
> Closed at: 2016-12-18 22:17:38 UTC  
> Url: https://github.com/boostorg/yap/pull/2  

I did this because I think a general CMake module to get disassembly is useful, and I might need it for Hana. If you don't like the idea, just discard the PR.  
  
Note that depending on the way you're using the disassembly-generating targets, we could also just make them dependencies of the `perf` target and get rid of this list of  
```cmake  
COMMAND ${CMAKE_COMMAND} --build ${CMAKE_BINARY_DIR} --target disassemble.eval_as_cpp_expr  
```

---

## Comment 1

> Username: ldionne  
> Created_at: 2016-12-18 22:13:49 UTC  
> Url: https://github.com/boostorg/yap/pull/2#issuecomment-267850831  

Should I be submitting my PRs against the `c++14` branch?

---

## Comment 2

> Username: tzlaine  
> Created_at: 2016-12-18 22:18:30 UTC  
> Url: https://github.com/boostorg/yap/pull/2#issuecomment-267851102  

No.  I'm in the process of merging that into master by hand, with #if guards.  I'm tired of maintaining it separately.

---

## Comment 3

> Username: ldionne  
> Created_at: 2016-12-18 22:19:17 UTC  
> Url: https://github.com/boostorg/yap/pull/2#issuecomment-267851154  

Alright. Then we can setup CI with and without C++1z, just to make sure everything works.

---

## Comment 4

> Username: tzlaine  
> Created_at: 2016-12-18 22:20:54 UTC  
> Url: https://github.com/boostorg/yap/pull/2#issuecomment-267851247  

Oh, and I should have said... Thanks for this PR.  I like it a lot.  I used the dependency trick on the perf target with those new disassemble targets locally, and it works quite well.

---

## Comment 5

> Username: tzlaine  
> Created_at: 2016-12-18 23:22:09 UTC  
> Url: https://github.com/boostorg/yap/pull/2#issuecomment-267854814  

Oh, and I just saw this last message; there is already CI for the c++14 branch: https://travis-ci.org/tzlaine/yap  
  
I couldn't get a late enough version of Clang to get the C++17 code working in CI though.

---
