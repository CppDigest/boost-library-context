# #2136 - Build error on ARM64-Release [Closed]

> Username: thorichelli  
> Created at: 2020-12-06 16:05:56 UTC  
> Updated at: 2021-05-29 22:14:41 UTC  
> Closed at: 2021-05-29 22:14:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2136  

### Version of Beast  
v124  
### Version of Boost  
1.65  
  
### Steps necessary to reproduce the problem  
  
just including the header is enough:  
  
`#include <boost/beast.hpp>`  
  
Severity	Code	Description	Project	File	Line	Suppression State  
Error	C3861	'__cpuid': identifier not found (compiling source file sources\test.cpp)	Lib	c:\src\beast\include\boost\beast\core\detail\cpu_info.hpp	49	  
  
Severity	Code	Description	Project	File	Line	Suppression State  
Error	LNK2001	unresolved external symbol jump_fcontext  libboost_coroutine-vc140-mt-1_65.lib(coroutine_context.obj)	  
  
### All relevant compiler information  
msvc 140 ARM64 Release  
  
It cannot find __cpuid on ARM64, how can I make it find? Why do I need cpu_info, can I skip it for a basic query operation?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-12-07 15:41:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2136#issuecomment-739997662  

looking around the internet, it seems we need to include <intrin.h> on windows  
https://docs.microsoft.com/en-us/cpp/intrinsics/cpuid-cpuidex?view=msvc-160

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2136#issuecomment-850857940  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: eata7  
> Created at: 2021-05-29 21:51:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2136#issuecomment-850904022  

I guess no

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-05-29 22:14:32 UTC  
> Url: https://github.com/boostorg/beast/issues/2136#issuecomment-850906593  

I believe this was fixed in a later version of Beast
