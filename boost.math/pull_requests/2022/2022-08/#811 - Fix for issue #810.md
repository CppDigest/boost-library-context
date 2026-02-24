# #811 Fix for issue #810 [Merged]

> Username: mborland  
> Created at: 2022-08-04 02:37:07 UTC  
> Updated at: 2022-08-04 15:31:16 UTC  
> Merged at: 2022-08-04 15:31:12 UTC  
> Closed at: 2022-08-04 15:31:12 UTC  
> Url: https://github.com/boostorg/math/pull/811  

Addresses 0 as null pointer constant and sign compare warnings as well.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-08-04 11:10:48 UTC  
> Url: https://github.com/boostorg/math/pull/811#issuecomment-1205111828  

The failure is unconnected but worrying (GCC-9 and gun++11 mode):  
  
```  
testing.capture-output ../../../bin.v2/libs/math/test/test_chatterjee_correlation.test/gcc-9/debug/link-static/threading-multi/visibility-hidden/test_chatterjee_correlation.run  
  
====== BEGIN OUTPUT ======  
  
Error at test_chatterjee_correlation.cpp:test_paper:130:  
  
  Mollified relative error in float precision is 0.0561, which exceeds 0.012, error/tol  = 4.68.  
  
  Expected: +0.828877568 = +0x1.a862a4p-1  
  
  Computed: +0.884999990 = +0x1.c51eb8p-1  
  
  ULP distance: +941578  
  
malloc(): invalid next size (unsorted)  
  
Aborted (core dumped)  
```

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2022-08-04 11:11:14 UTC  
> Url: https://github.com/boostorg/math/pull/811#issuecomment-1205112277  

At https://drone.cpp.al/boostorg/math/768/197/2

---

## Comment 3

> Username: jzmaddock  
> Created_at: 2022-08-04 12:39:05 UTC  
> Url: https://github.com/boostorg/math/pull/811#issuecomment-1205201311  

See also https://github.com/boostorg/math/pull/812 which extends this PR with further tests.  
(no reason not to go ahead and merge this one though).

---

## Comment 4

> Username: mborland  
> Created_at: 2022-08-04 15:30:44 UTC  
> Url: https://github.com/boostorg/math/pull/811#issuecomment-1205414520  

> The failure is unconnected but worrying (GCC-9 and gun++11 mode):  
>   
> ```  
> testing.capture-output ../../../bin.v2/libs/math/test/test_chatterjee_correlation.test/gcc-9/debug/link-static/threading-multi/visibility-hidden/test_chatterjee_correlation.run  
>   
> ====== BEGIN OUTPUT ======  
>   
> Error at test_chatterjee_correlation.cpp:test_paper:130:  
>   
>   Mollified relative error in float precision is 0.0561, which exceeds 0.012, error/tol  = 4.68.  
>   
>   Expected: +0.828877568 = +0x1.a862a4p-1  
>   
>   Computed: +0.884999990 = +0x1.c51eb8p-1  
>   
>   ULP distance: +941578  
>   
> malloc(): invalid next size (unsorted)  
>   
> Aborted (core dumped)  
> ```  
  
I think that test can be safely removed from CI testing. I'll look at it this week and open a PR for it.

---
