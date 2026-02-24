# #161 - Including only any_range.hpp of Boost 1.89 fails in const_reference_type_generator [Closed]

> Username: brandl-muc  
> Created at: 2025-12-19 15:04:58 UTC  
> Updated at: 2026-01-23 08:37:23 UTC  
> Closed at: 2026-01-23 08:37:23 UTC  
> Url: https://github.com/boostorg/range/issues/161  

Including `<boost/range/any_range.hpp>` leads to the following compiler error(s) when compiling with MSVC 17.14.9:  
```  
1>------ Build started: Project: BoostAnyRange, Configuration: Debug x64 ------  
1>BoostAnyRange.cpp  
1>D:\Projects\SO\EOSLibsInventoy\out\build\v143-x64-debug-ninja-vcpkg\vcpkg_installed\x64-windows-v143\include\boost\range\detail\any_iterator_interface.hpp(30,26): error C2146: syntax error: missing '>' before identifier 'add_const'  
1>(compiling source file 'BoostAnyRange.cpp')  
1>    D:\Projects\SO\EOSLibsInventoy\out\build\v143-x64-debug-ninja-vcpkg\vcpkg_installed\x64-windows-v143\include\boost\range\detail\any_iterator_interface.hpp(30,26):  
1>    the template instantiation context (the oldest one first) is  
1>        D:\Projects\SO\EOSLibsInventoy\out\build\v143-x64-debug-ninja-vcpkg\vcpkg_installed\x64-windows-v143\include\boost\range\detail\any_iterator_interface.hpp(26,16):  
1>        while compiling class template 'boost::range_detail::const_reference_type_generator'  
1>D:\Projects\SO\EOSLibsInventoy\out\build\v143-x64-debug-ninja-vcpkg\vcpkg_installed\x64-windows-v143\include\boost\range\detail\any_iterator_interface.hpp(30,35): error C2947: expecting '>' to terminate template-argument-list, found '<'  
1>(compiling source file 'BoostAnyRange.cpp')  
1>D:\Projects\SO\EOSLibsInventoy\out\build\v143-x64-debug-ninja-vcpkg\vcpkg_installed\x64-windows-v143\include\boost\range\detail\any_iterator_interface.hpp(28,30): error C2062: type '<error type>' unexpected  
1>(compiling source file 'BoostAnyRange.cpp')  
1>D:\Projects\SO\EOSLibsInventoy\out\build\v143-x64-debug-ninja-vcpkg\vcpkg_installed\x64-windows-v143\include\boost\range\detail\any_iterator_interface.hpp(32,20): error C2039: 'type': is not a member of '`global namespace''  
1>(compiling source file 'BoostAnyRange.cpp')  
1>D:\Projects\SO\EOSLibsInventoy\out\build\v143-x64-debug-ninja-vcpkg\vcpkg_installed\x64-windows-v143\include\boost\range\detail\any_iterator_interface.hpp(34,16): error C2039: 'type': is not a member of '`global namespace''  
1>(compiling source file 'BoostAnyRange.cpp')  
1>D:\Projects\SO\EOSLibsInventoy\out\build\v143-x64-debug-ninja-vcpkg\vcpkg_installed\x64-windows-v143\include\boost\range\detail\any_iterator_interface.hpp(34,25): error C2238: unexpected token(s) preceding ';'  
1>(compiling source file 'BoostAnyRange.cpp')  
1>Done building project "BoostAnyRange.vcxproj" -- FAILED.  
```  
  
The error happens independent of the `/std:c++` language standard used. This works with Boost 1.67.0 without any other changes.  
  
A MRE would be this:  
```  
#include <boost/range/any_range.hpp>  
  
int main()  
{}  
```

---

## Comment 1

> Username: brandl-muc  
> Created at: 2025-12-19 15:06:40 UTC  
> Url: https://github.com/boostorg/range/issues/161#issuecomment-3675418395  

Copilot suggests the following:  
> The error C2146: syntax error: missing '>' before identifier 'add_const' is caused because the add_const type trait is not found in the current scope. In Boost, it is defined in the header <boost/type_traits/add_const.hpp>.  
>* Make sure to include this header at the top of your file.  
>* Also, use the fully qualified name boost::add_const if not already in the boost namespace, to avoid ambiguity.  
>* No other code needs to be changed; just ensure the include is present and the correct namespace is used.  
  
And indeed, adding this include before `any_range.hpp` as in the following fixes the issue:  
```  
#include <boost/type_traits/add_const.hpp>  
#include <boost/range/any_range.hpp>  
  
int main()  
{}  
```

---

## Comment 2

> Username: brandl-muc  
> Created at: 2025-12-19 15:11:22 UTC  
> Url: https://github.com/boostorg/range/issues/161#issuecomment-3675433829  

A similar error happens with [gcc (trunk)](https://godbolt.org/z/hr1b6qb9M) and [clang (trunk)](https://godbolt.org/z/883Mzvzbo).  
Applying the suggested fix helps with [gcc](https://godbolt.org/z/saW3ehz9d) and [clang](https://godbolt.org/z/xPd6fonvM).

---

## Comment 3

> Username: adentinger  
> Created at: 2026-01-22 21:50:17 UTC  
> Url: https://github.com/boostorg/range/issues/161#issuecomment-3786918241  

Same as #157 ?  
  
P.S. I don't work here :)

---

## Comment 4

> Username: brandl-muc  
> Created at: 2026-01-23 08:36:21 UTC  
> Url: https://github.com/boostorg/range/issues/161#issuecomment-3789061849  

> Same as [#157](https://github.com/boostorg/range/pull/157) ?  
>   
> P.S. I don't work here :)  
  
Oh dang, yes, this is a duplicate, thanks. Shame on me.  
I'm closing this issue then.  
  
@adentinger Boost is an open source project, noone "works here". I'm not a maintainer either, I just reported the issue.

---

## Comment 5

> Username: brandl-muc  
> Created at: 2026-01-23 08:37:09 UTC  
> Url: https://github.com/boostorg/range/issues/161#issuecomment-3789065272  

Cannot close as duplicate because I cannot select #157
