# #112 Don't define BOOST_SP_HAS_GCC_INTRINSICS for MSVC [Merged]

> Username: masterleinad  
> Created at: 2024-04-12 13:14:35 UTC  
> Updated at: 2024-04-14 03:38:25 UTC  
> Merged at: 2024-04-13 18:41:02 UTC  
> Closed at: 2024-04-13 18:41:02 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/112  

Mirroring [dealii/dealii@`e969195` (#16621)](https://github.com/dealii/dealii/pull/16621/commits/e969195f058cfa491dad3580797b1190224ef4b9). We were running into errors like  
```  
D:\a\dealii\dealii\bundled\boost-1.84.0\include\boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp(35,5): error C3861: '__atomic_fetch_add': identifier not found [D:\a\dealii\dealii\build\source\numerics\object_numerics_debug.vcxproj]  
D:\a\dealii\dealii\bundled\boost-1.84.0\include\boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp(40,12): error C3861: '__atomic_fetch_sub': identifier not found [D:\a\dealii\dealii\build\source\numerics\object_numerics_debug.vcxproj]  
D:\a\dealii\dealii\bundled\boost-1.84.0\include\boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp(49,31): error C3861: '__atomic_load_n': identifier not found [D:\a\dealii\dealii\build\source\numerics\object_numerics_debug.vcxproj]  
D:\a\dealii\dealii\bundled\boost-1.84.0\include\boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp(58,13): error C3861: '__atomic_compare_exchange_n': identifier not found [D:\a\dealii\dealii\build\source\numerics\object_numerics_debug.vcxproj]  
D:\a\dealii\dealii\bundled\boost-1.84.0\include\boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp(67,12): error C3861: '__atomic_load_n': identifier not found [D:\a\dealii\dealii\build\source\numerics\object_numerics_debug.vcxproj]  
D:\a\dealii\dealii\bundled\boost-1.84.0\include\boost/smart_ptr/detail/spinlock_gcc_atomic.hpp(48,16): error C3861: '__atomic_test_and_set': identifier not found [D:\a\dealii\dealii\build\source\numerics\object_numerics_debug.vcxproj]  
D:\a\dealii\dealii\bundled\boost-1.84.0\include\boost/smart_ptr/detail/spinlock_gcc_atomic.hpp(61,9): error C3861: '__atomic_clear': identifier not found [D:\a\dealii\dealii\build\source\numerics\object_numerics_debug.vcxproj]  
```  
with MSVC, see https://github.com/dealii/dealii/pull/16621#issuecomment-1937412229 and https://github.com/dealii/dealii/issues/16413#issue-2065856458. It seems the compiler doesn't support `__atomic_*` intrinsics.

---

## Comment 1

> Username: pdimov  
> Created_at: 2024-04-12 13:34:53 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/112#issuecomment-2051775999  

I wonder what's the root cause for this, because MSVC doesn't define e.g. `__ATOMIC_RELAXED` (https://godbolt.org/z/os8sh5M5f). Why is it being defined in your case?

---

## Comment 2

> Username: bangerth  
> Created_at: 2024-04-12 14:52:33 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/112#issuecomment-2051914025  

@pdimov I don't actually know. It is certainly confusing to me too but it's a compiler I only have access to via a Continuous Integration setup, and so I can't find out :-( All I know is that it is indeed the compiler in that set-up that sets these `#define`s since it's neither BOOST nor our own cmake scripts. It *is* confusing to see that nobody else seems to be running into this problem -- as evidenced by the lack of github issues over the past few years, as well as the fact that one can't find anything online about the problem I'm seeing.  
  
I will note that the test as written does not actually check what you want to check. The test tests for compiler preprocessor defines like `__ATOMIC_RELAXED` and uses that to infer that the compiler supports GCC-style intrinsics. Assuming that only GCC-style compilers provide the intrinsics, there is probably little harm in adding `!defined(_MSVC)`.

---

## Comment 3

> Username: bangerth  
> Created_at: 2024-04-12 14:55:44 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/112#issuecomment-2051921174  

Actually, I will retract what I just said. I found where the `#define` is set: It is in the Kokkos library that apparently we `#include` first. In `./bundled/kokkos-3.7.00/tpls/desul/include/desul/atomics/Common.hpp` (and also `./bundled/kokkos-3.7.00/core/src/Cuda/Kokkos_Cuda_Atomic_Intrinsics.hpp`), there is this code:  
```  
#ifndef __ATOMIC_RELAXED  
#define __ATOMIC_RELAXED 0  
#define __ATOMIC_CONSUME 1  
#define __ATOMIC_ACQUIRE 2  
#define __ATOMIC_RELEASE 3  
#define __ATOMIC_ACQ_REL 4  
#define __ATOMIC_SEQ_CST 5  
#endif  
```

---

## Comment 4

> Username: masterleinad  
> Created_at: 2024-04-12 15:00:03 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/112#issuecomment-2051930228  

Fixed in Kokkos 4.1.00 (https://github.com/kokkos/kokkos/pull/5804).

---

## Comment 5

> Username: pdimov  
> Created_at: 2024-04-12 15:10:00 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/112#issuecomment-2051950985  

> Assuming that only GCC-style compilers provide the intrinsics, there is probably little harm in adding `!defined(_MSVC)`.  
  
At the moment `clang-cl` takes this path as it supports the GCC intrinsics, and it does define `_MSC_VER`.  
  
This is likely to become moot soon though, as I'll be removing the platform-specific implementations in favor of always using the standard `<atomic>` as C++11 will be required in 1.86.

---

## Comment 6

> Username: bangerth  
> Created_at: 2024-04-12 17:01:57 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/112#issuecomment-2052137670  

@pdimov The flag to use C++11 features instead of compiler intrinsics is not well documented, and I couldn't find much about it when searching online. Is your recommendation to just set this flag and not use intrinsics?

---

## Comment 7

> Username: pdimov  
> Created_at: 2024-04-13 15:04:46 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/112#issuecomment-2053671719  

No, I never intended to make such a recommendation; from the closing of this PR I infer that you'll fix the problem by upgrading Kokkos?

---

## Comment 8

> Username: masterleinad  
> Created_at: 2024-04-13 17:01:28 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/112#issuecomment-2053702206  

> No, I never intended to make such a recommendation; from the closing of this PR I infer that you'll fix the problem by upgrading Kokkos?  
  
We patched the bundled `Kokkos` and it's fixed in newer versions. deal.II users could still run into problems if using an external Kokkos version before 4.1.00 and an external boost version. If you are willing to accept this patch that would help at least with upcoming boost versions.

---

## Comment 9

> Username: bangerth  
> Created_at: 2024-04-14 03:26:01 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/112#issuecomment-2053882787  

@pdimov I inferred that you didn't intend to make such a recommendation in any of the comments above. But is that your recommendation anyway (independent of the problem this PR was about)? At least for reasonably modern compilers, one would expect C++11 atomics to be implemented correctly.

---

## Comment 10

> Username: pdimov  
> Created_at: 2024-04-14 03:38:25 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/112#issuecomment-2053885850  

The plan at the moment is to make C++11 a requirement in 1.86, and if/when I do that, I'll make the C++11 atomics the default, so you shouldn't need to do anything on your side.

---
