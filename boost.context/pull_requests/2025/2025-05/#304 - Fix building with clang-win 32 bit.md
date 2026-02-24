# #304 Fix building with clang-win 32 bit. [Merged]

> Username: grafikrobot  
> Created at: 2025-05-16 03:02:23 UTC  
> Updated at: 2025-05-25 14:30:19 UTC  
> Merged at: 2025-05-25 14:30:12 UTC  
> Closed at: 2025-05-25 14:30:12 UTC  
> Url: https://github.com/boostorg/context/pull/304  

When building with address-model 32 and clang-win building will fail because it misses adding the /safeseh flag as it does for bare msvc. This adds the option to mirror the msvc building. It also adds a CI build to test the clang-win/32 variation.

---

## Comment 1

> Username: olk  
> Created_at: 2025-05-25 14:30:18 UTC  
> Url: https://github.com/boostorg/context/pull/304#issuecomment-2907859105  

ty

---
