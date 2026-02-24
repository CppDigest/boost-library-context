# #812 fixes radix sort error [Merged]

> Username: rosenrodt  
> Created at: 2019-01-15 04:40:31 UTC  
> Updated at: 2019-01-21 11:30:29 UTC  
> Merged at: 2019-01-21 11:30:29 UTC  
> Closed at: 2019-01-21 11:30:29 UTC  
> Url: https://github.com/boostorg/compute/pull/812  

insert memory fence to avoid AMD OpenCL compiler reordering the read/writes; fixes issue #811  
  
CTest log for Vega56  
  
```  
99% tests passed, 2 tests failed out of 169  
   
Total Test time (real) =  85.07 sec  
  
The following tests FAILED:  
 	143 - misc.amd_cpp_kernel_language (Failed)  
 	148 - example.amd_cpp_kernel (Exit code 0xc0000409)  
Errors while running CTest  
```

---

## Comment 1

> Username: rosenrodt  
> Created_at: 2019-01-15 08:03:37 UTC  
> Url: https://github.com/boostorg/compute/pull/812#issuecomment-454300248  

I am closing this PR because some issues still spill over after a clean rebuild on my test machine. Will reopen when it's done

---

## Comment 2

> Username: coveralls  
> Created_at: 2019-01-15 20:59:16 UTC  
> Updated_at: 2019-01-19 01:21:21 UTC  
> Url: https://github.com/boostorg/compute/pull/812#issuecomment-454548975  

[![Coverage Status](https://coveralls.io/builds/21162731/badge)](https://coveralls.io/builds/21162731)  
  
Coverage remained the same at 84.02% when pulling **81dc8c73528cd61b9e4220befcec522ee06c9341 on rosenrodt:sort-failure-on-amd-vega** into **2135633bc7f6bddaf09ae409a63a83174e89c881 on boostorg:develop**.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2019-01-17 07:33:31 UTC  
> Url: https://github.com/boostorg/compute/pull/812#issuecomment-455070631  

You reopened the PR, but the code is the same. How is `mem_fence` related to what you wrote in https://github.com/boostorg/compute/issues/811#issuecomment-454347805?

---

## Comment 4

> Username: rosenrodt  
> Created_at: 2019-01-17 17:57:23 UTC  
> Url: https://github.com/boostorg/compute/pull/812#issuecomment-455267767  

I closed and reopened the PR because I was unsure of the cause of failed assertions when invoking `is_sorted()` on sorted arrays, but later figured it was totally unrelated to radix sort. Details were described in my comment [here](https://github.com/boostorg/compute/issues/811#issuecomment-454347805)  
  
The PR is to make sure the memory access is in correct order and not mangled up by the compiler, therefore the `mem_fence` in [line 179](https://github.com/boostorg/compute/pull/812/files#diff-a3cb7a47c4aebcbfb2d5c01dca8995f6R179). But I rarely encounter such a case where compiler is not respecting obvious variable dependencies. So this is just an ad hoc fix until maybe we can get AMD to look into this, especially by the fact that it only happens on AMD Vega/RX5xx and not the older chips nor chips from other vendors.   
  
Another `mem_fence` is inserted for no apparent reason because it has to be like this to work on Vegas.

---

## Comment 5

> Username: jszuppe  
> Created_at: 2019-01-18 09:31:08 UTC  
> Url: https://github.com/boostorg/compute/pull/812#issuecomment-455482684  

Hmm.. At least a fence for global memory would make more sense, does that option work too?

---

## Comment 6

> Username: rosenrodt  
> Created_at: 2019-01-18 10:51:49 UTC  
> Url: https://github.com/boostorg/compute/pull/812#issuecomment-455506328  

Fences for global, local or both also work. So it won't make a difference. But I think you're right on that: global fencing is more appropriate in literal sense

---

## Comment 7

> Username: jszuppe  
> Created_at: 2019-01-18 10:59:29 UTC  
> Url: https://github.com/boostorg/compute/pull/812#issuecomment-455508429  

If I'm correct this kernel is run with one thread (via `enqueue_task`), so those fences should not have any performance cost. Because of that it shouldn't be a problem to include that workaround after:  
* You change fence flag to global memory  
* In those lines add reference to #811 bug, for example `// workaround for Radeon RX Vega bug, see #811`.

---

## Comment 8

> Username: rosenrodt  
> Created_at: 2019-01-21 10:34:33 UTC  
> Url: https://github.com/boostorg/compute/pull/812#issuecomment-456026257  

bump 😃   
@jszuppe anything else that needs to be done?

---

## Comment 9

> Username: jszuppe  
> Created_at: 2019-01-21 11:30:19 UTC  
> Url: https://github.com/boostorg/compute/pull/812#issuecomment-456042535  

No, it's ok. I just didn't have time to look at it.

---
