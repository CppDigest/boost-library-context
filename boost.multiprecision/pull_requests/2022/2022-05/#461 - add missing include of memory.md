# #461 add missing include of memory [Merged]

> Username: svigerske  
> Created at: 2022-05-17 13:38:09 UTC  
> Updated at: 2022-05-18 17:24:42 UTC  
> Merged at: 2022-05-18 17:23:23 UTC  
> Closed at: 2022-05-18 17:23:23 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/461  

With GCC 12, header memory is included by less other system headers: https://gcc.gnu.org/gcc-12/porting_to.html  
  
However, it is required in float128.hpp due to the use of `std::unique_ptr`.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2022-05-17 15:22:32 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/461#issuecomment-1129006205  

Thanks @svigerske , we have another PR adding gcc-12 and clang-14 CI support which would no doubt pick this up, unfortunately all we see are pink-unicorns and no run logs at present :(

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2022-05-18 17:23:09 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/461#issuecomment-1130285932  

See also #462

---

## Comment 3

> Username: ckormanyos  
> Created_at: 2022-05-18 17:24:41 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/461#issuecomment-1130287239  

Thanks for this catch @svigerske.  
  
I decided to simply merge this one, then let CI run. After that, I'll add GCC12/clang++ on Jammy in a separate PR. Well, maybe this wasn't the easiest path (from my side) for this combined effort, but slow and steady it'll work.

---
