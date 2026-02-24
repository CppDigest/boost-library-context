# #170 Arenoros/fix build on pre cxx11 compilers [Closed]

> Username: Arenoros  
> Created at: 2024-02-21 10:42:15 UTC  
> Updated at: 2024-02-22 10:12:49 UTC  
> Closed at: 2024-02-22 08:13:14 UTC  
> Url: https://github.com/boostorg/charconv/pull/170  

let me finalize my theories, because it is not realistic for me to deploy all tests locally, I just want to achieve that all tests pass successfully, and the further decision to include or not is not very important to me.

---

## Comment 1

> Username: Arenoros  
> Created_at: 2024-02-21 12:24:18 UTC  
> Url: https://github.com/boostorg/charconv/pull/170#issuecomment-1956538192  

In the end, all the edits consist in the way to inline 2 arrays with uint128 **main_cache_holder::cache[]** and **cache_holder_ieee754_binary64::cache[]**, which is solved by simply removing their declaration from the body of the struct and disable warnings in tests as it is done for all other versions of GCC, I really did not understand why there is a check for GCC >= 5 when defined(__GNUC__) as in the case of clang is enough.  
  
I hope you decide to include this in the main branch, I'm not asking to maintain gcc4.8 but I think these changes don't affect the whole core code of the library.

---

## Comment 2

> Username: mborland  
> Created_at: 2024-02-22 08:13:14 UTC  
> Url: https://github.com/boostorg/charconv/pull/170#issuecomment-1958915561  

> In the end, all the edits consist in the way to inline 2 arrays with uint128 **main_cache_holder::cache[]** and **cache_holder_ieee754_binary64::cache[]**, which is solved by simply removing their declaration from the body of the struct  
  
You also made them non-constexpr. Doesn't this push the first construction to run-time? That is a non-trivial operation.  
  
> I really did not understand why there is a check for GCC >= 5 when defined(**GNUC**) as in the case of clang is enough.  
>   
  
You would need to reorder the checks, or make sure `__clang__` is not defined since `__GNUC__` is equal to 4 on clang platforms.   
  
> I hope you decide to include this in the main branch, I'm not asking to maintain gcc4.8 but I think these changes don't affect the whole core code of the library.  
  
I still don't see any benefit to merging. If this is accepted without any continued testing for GCC 4.8 it is likely that platform will break again down the road. The code as-is supports all of the platforms I intend to support, and I believe the difference in construction of the tables would make things a bit worse on those platforms.

---

## Comment 3

> Username: Arenoros  
> Created_at: 2024-02-22 10:12:16 UTC  
> Updated_at: 2024-02-22 10:12:49 UTC  
> Url: https://github.com/boostorg/charconv/pull/170#issuecomment-1959116587  

Yes, I agree, I checked on godbolt, if remove constexpr the compiler inserts an unnecessary table jump.

---
