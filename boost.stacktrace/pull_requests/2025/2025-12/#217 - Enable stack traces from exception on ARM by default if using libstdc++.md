# #217 Enable stack traces from exception on ARM by default if using libstdc++. [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2025-12-10 17:05:06 UTC  
> Updated at: 2025-12-24 17:05:07 UTC  
> Closed at: 2025-12-24 17:05:07 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/217  

We know the implementation works on any architecture if libstdc++ is used (leaks are with libc++). So enable it by default in that case, even if not using x86.  
  
Related to #163

---

## Comment 1

> Username: apolukhin  
> Created_at: 2025-12-23 17:22:51 UTC  
> Url: https://github.com/boostorg/stacktrace/pull/217#issuecomment-3687412770  

That won't help. The check should make sure that the exceptions are thrown by `libgcc_s` rather than by `libc++abi`.  
  
Please take a look at https://github.com/boostorg/stacktrace/pull/220  
Does it solve the issue you are encountering?

---
