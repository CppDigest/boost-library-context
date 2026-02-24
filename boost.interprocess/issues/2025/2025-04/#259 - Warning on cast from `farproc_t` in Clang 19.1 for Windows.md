# #259 - Warning on cast from `farproc_t` in Clang 19.1 for Windows [Closed]

> Username: joaquintides  
> Created at: 2025-04-12 18:03:24 UTC  
> Updated at: 2025-04-14 19:44:06 UTC  
> Closed at: 2025-04-14 19:07:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/259  

This problem arises on Boost.Unordered tests with Clang 19.1 for Visual Studio 2022 v17.13.3 (`clang-cl`). Full logs [here](https://github.com/boostorg/unordered/actions/runs/13816411307/job/38650945465).  
  
```  
2025-03-12T16:47:36.5368174Z .\boost/interprocess/detail/win32_api.hpp(1122,59): error: cast from 'farproc_t' (aka 'int (*)() __attribute__((stdcall))') to 'NtQuerySystemInformation_t' (aka 'long (*)(int, void *, unsigned long, unsigned long *) __attribute__((stdcall))') converts to incompatible function type [-Werror,-Wcast-function-type-mismatch]  
2025-03-12T16:47:36.5371616Z  1122 |    NtQuerySystemInformation_t pNtQuerySystemInformation = reinterpret_cast<NtQuerySystemInformation_t>  
2025-03-12T16:47:36.5373049Z       |                                                           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
2025-03-12T16:47:36.5373933Z  1123 |          (dll_func::get(dll_func::NtQuerySystemInformation));  
```  
  
(Same error happens at lines 1122, 1546, 1559, 1570, 1329, 1331, 1403, 1404, 1329 of `boost/interprocess/detail/win32_api.hpp`. ) This seems to be connected to this [issue](https://lists.llvm.org/pipermail/libcxx-commits/2024-May/083538.html): if this is the case, an intermediate cast tp `void *` should suffice to silent the warning.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2025-04-14 19:07:51 UTC  
> Url: https://github.com/boostorg/interprocess/issues/259#issuecomment-2802728134  

Let's see if this fixes most occurrences of the problem, instead of that single one...

---

## Comment 2

> Username: pdimov  
> Created at: 2025-04-14 19:44:04 UTC  
> Url: https://github.com/boostorg/interprocess/issues/259#issuecomment-2802819883  

I brought this up in https://github.com/llvm/llvm-project/pull/86131 and we'll hopefully see that fixed on the Clang side in https://github.com/llvm/llvm-project/pull/135660.
