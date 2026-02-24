# #429 Fix multiplicity value comparison [Merged]

> Username: jan-grimo  
> Created at: 2025-05-01 14:33:42 UTC  
> Updated at: 2025-05-12 10:02:11 UTC  
> Merged at: 2025-05-12 08:55:20 UTC  
> Closed at: 2025-05-12 08:55:20 UTC  
> Url: https://github.com/boostorg/graph/pull/429  

Replacement of at() in 8f3139be as part of #349 was faulty: Iterator deref is a (key, value) pair, but only the value should be compared  
  
Closes #428   
  
- Fixes bug in `compare_multiplicity::operator bool`  
- Adds test case from #428 into isomorphism test set

---

## Comment 1

> Username: jan-grimo  
> Created_at: 2025-05-01 18:16:48 UTC  
> Url: https://github.com/boostorg/graph/pull/429#issuecomment-2845413393  

@jeremy-murphy can you help me out with the failing CI tests? I'm not sure what's wrong, perhaps it's the use of the graphviz reader? Is there a better way I should be embedding the sample graphs from the reporting issue?

---

## Comment 2

> Username: jeremy-murphy  
> Created_at: 2025-05-01 22:10:04 UTC  
> Url: https://github.com/boostorg/graph/pull/429#issuecomment-2845890282  

Yes, there is a problem with the CI that I'll fix today.

---

## Comment 3

> Username: jeremy-murphy  
> Created_at: 2025-05-02 04:37:35 UTC  
> Updated_at: 2025-05-02 04:37:51 UTC  
> Url: https://github.com/boostorg/graph/pull/429#issuecomment-2846295565  

Looks like there might also actually be a problem with file access in your new test on macOS?  
```  
====== BEGIN OUTPUT ======  
Elapsed time (clock cycles): 1050  
Elapsed time (clock cycles): 1776  
libc++abi: terminating due to uncaught exception of type std::runtime_error: Error: Cannot open file   
  
EXIT STATUS: 134  
====== END OUTPUT ======  
```

---

## Comment 4

> Username: jeremy-murphy  
> Created_at: 2025-05-05 21:36:06 UTC  
> Url: https://github.com/boostorg/graph/pull/429#issuecomment-2852393337  

The CI is fixed now, so please merge the tip of `develop` in and you should get a green build.

---

## Comment 5

> Username: jeremy-murphy  
> Created_at: 2025-05-07 00:43:41 UTC  
> Url: https://github.com/boostorg/graph/pull/429#issuecomment-2856690280  

Looks like a problem with your change?

---

## Comment 6

> Username: jeremy-murphy  
> Created_at: 2025-05-09 07:34:25 UTC  
> Url: https://github.com/boostorg/graph/pull/429#issuecomment-2865471994  

@jan-grimo ping.

---

## Comment 7

> Username: jeremy-murphy  
> Created_at: 2025-05-12 10:02:10 UTC  
> Url: https://github.com/boostorg/graph/pull/429#issuecomment-2871875837  

Thanks for fixing it promptly!

---
