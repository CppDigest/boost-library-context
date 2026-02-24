# #236 - Floating point inaccuracies in `get_current_process_creation_time` lead to `intermodule_singleton` failures [Closed]

> Username: joaquintides  
> Created at: 2024-10-18 17:24:30 UTC  
> Updated at: 2024-12-13 19:56:17 UTC  
> Closed at: 2024-10-19 07:31:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/236  

I've been investigating the following errors in Boost.Flyweight tests for GCC/MinGW:  
https://github.com/boostorg/flyweight/actions/runs/11307014518/job/31448262434  
which are due to `intermodule_holder` (a mere wrapper over `intermodule_singleton`) creating duplicated instances of an object between and EXE and its linked DLL, when it should be the same instance that is used throughout the process.  
  
The bug boils down to  `intermodule_singleton` using `get_current_process_creation_time` to create computer-wide unique names for its managed resources:  
  
https://github.com/boostorg/interprocess/blob/75c3c9ef0a3d8ae2cd90bbf3af438a13913319cd/include/boost/interprocess/detail/os_thread_functions.hpp#L223-L234  
  
Local traces on the failing test show this:  
```  
CreationTime: 31138173, 3329082212  
sem_map: bipc_gmap_sem_map_5088_13373743802.127241  
CreationTime: 31138173, 3329082212  
sem_map: bipc_gmap_sem_map_5088_13373743802.127242  
```  
Note the difference in the last digit of `sem_map` despite `CreationTime` being the same (as it should): this is due to floating point codegen differences in `get_current_process_creation_time` when compiled for the EXE and the DLL. Robust avoidance of this problem should probably eliminate usage of floating point ops altogether.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-10-18 17:29:00 UTC  
> Url: https://github.com/boostorg/interprocess/issues/236#issuecomment-2422931353  

Maybe just return uint64_t in nanoseconds?

---

## Comment 2

> Username: igaztanaga  
> Created at: 2024-10-18 18:28:23 UTC  
> Url: https://github.com/boostorg/interprocess/issues/236#issuecomment-2423017165  

Shouldn't floating point rounding be deterministic?

---

## Comment 3

> Username: pdimov  
> Created at: 2024-10-18 18:33:00 UTC  
> Url: https://github.com/boostorg/interprocess/issues/236#issuecomment-2423024610  

There are enough issues with optimization to make it not (e.g. because intermediate calculations can be carried out in higher precision.) It's better to avoid floating point.

---

## Comment 4

> Username: igaztanaga  
> Created at: 2024-10-18 21:39:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/236#issuecomment-2423268913  

I still can't understand how floating point operations are not deterministic according, say, to the IEEE standard given the same result with the same inputs....  
  
Anyway, as Peter suggested, this commit in develop might solve the issue: https://github.com/boostorg/interprocess/commit/39705606e36beb45735e3254989a34dba69dfe96  
  
Let me know if this works...

---

## Comment 5

> Username: joaquintides  
> Created at: 2024-10-19 07:31:22 UTC  
> Updated at: 2024-10-19 07:32:28 UTC  
> Url: https://github.com/boostorg/interprocess/issues/236#issuecomment-2423654644  

I confirm the problem is solved, thank you!

---

## Comment 6

> Username: valiko-ua  
> Created at: 2024-12-13 19:14:25 UTC  
> Updated at: 2024-12-13 19:56:17 UTC  
> Url: https://github.com/boostorg/interprocess/issues/236#issuecomment-2542089393  

> I still can't understand how floating point operations are not deterministic according, say, to the IEEE standard given the same result with the same inputs....  
  
Maybe between first and second call to _get_current_process_creation_time_ someone changed floating-point rounding direction by calling _std::fesetround_.  
For example: https://godbolt.org/z/daGf875xE
