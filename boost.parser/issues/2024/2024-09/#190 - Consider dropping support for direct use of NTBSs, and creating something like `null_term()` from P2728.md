# #190 - Consider dropping support for direct use of NTBSs, and creating something like `null_term()` from P2728 [Closed]

> Username: tzlaine  
> Created at: 2024-09-29 19:50:48 UTC  
> Updated at: 2024-10-03 01:23:49 UTC  
> Closed at: 2024-10-03 01:23:49 UTC  
> Url: https://github.com/boostorg/parser/issues/190  

The latest draft of P2728 contains `null_term()`, which just returns `std::ranges::subrange(ptr, std::null_terminator)`.  If this is the way idiomatic C++ is going to be written, this library should do the same.  This change should wait until LEWG have seen this version of P2728 and approved of the approach.

---

## Comment 1

> Username: tzlaine  
> Created at: 2024-09-30 05:00:41 UTC  
> Url: https://github.com/boostorg/parser/issues/190#issuecomment-2382091000  

This would address #175.
