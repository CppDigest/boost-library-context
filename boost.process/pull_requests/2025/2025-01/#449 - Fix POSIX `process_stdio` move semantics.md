# #449 Fix POSIX `process_stdio` move semantics [Merged]

> Username: jgreitemann  
> Created at: 2025-01-12 19:34:17 UTC  
> Updated at: 2025-01-13 08:32:29 UTC  
> Merged at: 2025-01-13 00:13:34 UTC  
> Closed at: 2025-01-13 00:13:34 UTC  
> Url: https://github.com/boostorg/process/pull/449  

Fixes #448. Aside from adding the move operations, I've also added two test cases: one demonstrating implicit creation of pipes by `process_stdio` which was previously untested AFAICT, and one (regression) test specifically for the move semantics.  
  
I'm curious if you'd also be open to making the members for `process_io_binding` private (on both platforms, actually)? `process_io_binding` has to maintain an invariant (whether `fd` needs to be closed) and leaving its members public allows external code to break that invariant. I'm not sure that your policy is towards breaking source compatibility in the `detail` namespace, so I have refrained from making that change.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2025-01-12 23:38:14 UTC  
> Url: https://github.com/boostorg/process/pull/449#issuecomment-2585966195  

Re private: unix users do interesting things with FDs that I cannot anticipate.  Modifying these member is discouraged by the detail namespace, so it is a bit lf a "I know what I am doing" if someone accesses this.

---

## Review 2 [Approved]

> Username: klemens-morgenstern  
> Created_at: 2025-01-12 23:38:52 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/process/pull/449#pullrequestreview-2545555062  

Looks good.

---

## Comment 3

> Username: klemens-morgenstern  
> Created_at: 2025-01-12 23:39:44 UTC  
> Url: https://github.com/boostorg/process/pull/449#issuecomment-2585966662  

FreeBSD is a broken CI setup.

---

## Comment 4

> Username: klemens-morgenstern  
> Created_at: 2025-01-13 00:08:14 UTC  
> Url: https://github.com/boostorg/process/pull/449#issuecomment-2585977764  

The windows test on GHA is funny:  
  
```cpp  
 libs/process/test/v2/ext.cpp(116): error: in "ext/test_cwd_exe": check tt == tmp has failed [C:\Users\runneradmin\AppData\Local\Temp != "C:\Users\RUNNER~1\AppData\Local\Temp"]  
```  
  
But unrelated to this PR, so not a blocker.

---

## Comment 5

> Username: jgreitemann  
> Created_at: 2025-01-13 08:29:30 UTC  
> Url: https://github.com/boostorg/process/pull/449#issuecomment-2586480103  

Thanks for reviewing this so timely.  
  
I don't know about other issues in the FreeBSD pipeline, but it seems I made it worse because now there's a warning (as error) for the (intentional) pessimizing move.

---

## Comment 6

> Username: klemens-morgenstern  
> Created_at: 2025-01-13 08:32:28 UTC  
> Url: https://github.com/boostorg/process/pull/449#issuecomment-2586485303  

The freeBSD one was broken for non boost-process reasons (couldn't load DLL, some install mismatch).  
  
There is a warning, but I honestly don't care for warnings in tests. If you want to fix that with a #pragma or some other way, I'm happy to merge.

---
