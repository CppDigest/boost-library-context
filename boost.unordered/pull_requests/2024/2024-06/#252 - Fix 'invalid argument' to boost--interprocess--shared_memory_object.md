# #252 Fix 'invalid argument' to boost::interprocess::shared_memory_object [Merged]

> Username: k3DW  
> Created at: 2024-06-07 05:25:11 UTC  
> Updated at: 2024-07-08 17:32:37 UTC  
> Merged at: 2024-06-08 09:17:42 UTC  
> Closed at: 2024-06-08 09:17:42 UTC  
> Url: https://github.com/boostorg/unordered/pull/252  

Closes #251

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-06-07 05:33:04 UTC  
> Url: https://github.com/boostorg/unordered/pull/252#issuecomment-2154089906  

Amazing. Where does the `+` sign come from?

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-06-07 05:35:32 UTC  
> Url: https://github.com/boostorg/unordered/pull/252#issuecomment-2154096675  

Oh I see it, `stdlib-libc++` in path.

---

## Comment 3

> Username: cmazakas  
> Created_at: 2024-06-07 15:32:30 UTC  
> Url: https://github.com/boostorg/unordered/pull/252#issuecomment-2155076334  

Huh, how come this wasn't failing earlier? I feel like we were supposed to have been testing with libc++ and sanitizers for awhile, at least.

---
