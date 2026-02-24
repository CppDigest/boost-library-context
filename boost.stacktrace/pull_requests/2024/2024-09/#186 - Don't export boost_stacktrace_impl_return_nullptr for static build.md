# #186 Don't export boost_stacktrace_impl_return_nullptr for static build [Merged]

> Username: huangqinjin  
> Created at: 2024-09-13 16:52:18 UTC  
> Updated at: 2024-09-13 17:46:59 UTC  
> Merged at: 2024-09-13 17:42:31 UTC  
> Closed at: 2024-09-13 17:42:31 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/186  

Now `boost_stacktrace.lib` provides the symbol `boost_stacktrace_impl_return_nullptr` for static and shared build.  
  
For header only library, to make the symbol available **automatically**, the only way AFAIK is to export it. We may also consider provide a configuration macro to opt-out the exported symbol, and user need to manually define the non-exported symbol somewhere in their binaries.  
  
Closes #177 .

---

## Comment 1

> Username: coveralls  
> Created_at: 2024-09-13 17:20:33 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/186#issuecomment-2349534572  

[![Coverage Status](https://coveralls.io/builds/69789784/badge)](https://coveralls.io/builds/69789784)  
  
coverage: 86.35%. remained the same  
when pulling **2feb0fcf3c1591ae5c6810697b14ab3f06d8027d on huangqinjin:msvc-stacktrace-from-current-exception**  
into **3f79aee92fa93a024255ec1dd1965fffd7d3cf37 on boostorg:develop**.

---

## Comment 2

> Username: apolukhin  
> Created_at: 2024-09-13 17:45:31 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/186#issuecomment-2349639426  

Many thanks for the PR!

---
