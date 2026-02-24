# #188 resolving argument usage check in examples/performance [Merged]

> Username: jeffreyworley  
> Created at: 2023-08-08 03:16:23 UTC  
> Updated at: 2023-08-08 08:17:03 UTC  
> Merged at: 2023-08-08 08:17:03 UTC  
> Closed at: 2023-08-08 08:17:03 UTC  
> Url: https://github.com/boostorg/locale/pull/188  

The check for the correct number of arguments for perf_collate.cpp, perf_convert.cpp, and perf_format.cpp did not match the indices of `argv` that are accessed. This resulted in a crash if the previous check was satisfied on access to `argv[2]`. This change allows for backend and locale options to be provided as intended.

---

## Review 1 [Approved]

> Username: Flamefire  
> Created_at: 2023-08-08 07:14:26 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/locale/pull/188#pullrequestreview-1566627494  

You are right, thanks for fixing!

---
