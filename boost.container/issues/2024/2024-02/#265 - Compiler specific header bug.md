# #265 - Compiler specific header bug [Closed]

> Username: serfcity  
> Created at: 2024-02-05 18:34:36 UTC  
> Updated at: 2024-04-16 09:56:35 UTC  
> Closed at: 2024-04-16 09:56:34 UTC  
> Url: https://github.com/boostorg/container/issues/265  

```  
D:\boost_1_83_0\boost\container\detail\workaround.hpp(159): Error! E135: col(41) 'friend', 'virtual' or 'inline' modifiers may only be used on functions  
D:\boost_1_83_0\boost\container\detail\workaround.hpp(159): Note! N393: col(41) included from D:\boost_1_83_0\boost\container\vector.hpp(23)  
D:\boost_1_83_0\boost\container\detail\workaround.hpp(159): Note! N393: col(41) included from main.cpp(5)  
D:\boost_1_83_0\boost\container\detail\workaround.hpp(159): Error! E408: col(41) function templates can only name functions  
D:\boost_1_83_0\boost\container\detail\workaround.hpp(159): Error! E336: col(41) declaration specifiers are required to declare 'constexpr'  
D:\boost_1_83_0\boost\container\detail\workaround.hpp(159): Error! E006: col(41) syntax error; probable cause: missing ';'  
D:\boost_1_83_0\boost\container\detail\placement_new.hpp(18): Error! E498: col(49) syntax error before 'boost_container_new_t'; probable cause: incorrectly spelled type name  
D:\boost_1_83_0\boost\move\detail\placement_new.hpp(24): Error! E498: col(49) syntax error before 'boost_move_new_t'; probable cause: incorrectly spelled type name  
D:\boost_1_83_0\boost\cstdint.hpp(388): Error! E927: col(13) syntax error: '::intptr_t' has not been declared  
D:\boost_1_83_0\boost\cstdint.hpp(389): Error! E927: col(13) syntax error: '::uintptr_t' has not been declared  
D:\boost_1_83_0\boost\move\algo\detail\adaptive_sort_merge.hpp(96): Error! E121: col(1) syntax error  
D:\boost_1_83_0\boost\range\detail\implementation_help.hpp(63): Error! E408: col(25) function templates can only name functions  
D:\boost_1_83_0\boost\range\detail\implementation_help.hpp(63): Error! E336: col(25) declaration specifiers are required to declare 'constexpr'  
D:\boost_1_83_0\boost\range\detail\implementation_help.hpp(63): Error! E006: col(25) syntax error; probable cause: missing ';'  
D:\boost_1_83_0\boost\foreach_fwd.hpp(31): Error! E360: col(61) typedef 'boost_foreach_argument_dependent_lookup_hack' defined without an explicit type  
D:\boost_1_83_0\boost\foreach_fwd.hpp(31): Error! E006: col(58) syntax error; probable cause: missing ';'  
D:\boost_1_83_0\boost\foreach_fwd.hpp(49): Error! E121: col(1) syntax error  
```  
OpenWatcom v2 compiler, witch support C++98, doesn't work correctly with Boost.Container library.  
It looks like [this string](https://github.com/boostorg/container/blob/6e697d796897b32b471b4f0740dcaa03d8ee57cc/include/boost/container/detail/workaround.hpp#L162) (In Boost 1.83 it's line 159, but in current version it's 162) contain macros, witch haven't been correctly set for OpenWatcom v2.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-04-16 09:56:34 UTC  
> Url: https://github.com/boostorg/container/issues/265#issuecomment-2058701984  

Sorry, but OpenWatcom is not a supported compiler in Boost, it's not a Boost.Container specific issue, those macros come from the general Boost support library (Boost.Config).
