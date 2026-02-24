# #62 - warning: ISO C++ prohibits anonymous structs [-Wpedantic] [Closed]

> Username: viboes  
> Created at: 2017-10-14 10:59:09 UTC  
> Updated at: 2017-11-27 21:07:07 UTC  
> Closed at: 2017-10-24 17:19:56 UTC  
> Url: https://github.com/boostorg/winapi/issues/62  

```  
..\..\../boost/detail/winapi/system.hpp:54:9: warning: ISO C++ prohibits anonymous structs [-Wpedantic]  
         } DUMMYSTRUCTNAME;  
         ^  
  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2017-10-19 00:09:24 UTC  
> Url: https://github.com/boostorg/winapi/issues/62#issuecomment-337762637  

The unnamed structs are used by Windows SDK to achieve a specific binary layout and member access syntax. Naming the structs will change the syntax to access the members, so it will be a breaking change.  
  
The only way I know to get rid of the warning is to silence it, but the option in the compiler message is `-Wpedantic`, which is a blanket option, which supposedly corresponds to a list of more fine grained options. Do you know what specific option controls this warning?

---

## Comment 2

> Username: viboes  
> Created at: 2017-10-19 16:36:33 UTC  
> Url: https://github.com/boostorg/winapi/issues/62#issuecomment-337965314  

Unfortunately not. Otherwise I would disable it independently.  
  
If we can not do anything, please be free to close it as wontfix.

---

## Comment 3

> Username: Kojoley  
> Created at: 2017-11-13 18:31:52 UTC  
> Url: https://github.com/boostorg/winapi/issues/62#issuecomment-344013471  

There is no specific switch in GCC, you should push/ignore/pop `-Wpedantic`  
Clang `-Wgnu-anonymous-struct`  
MSVC (before VS2017) `C4094` https://msdn.microsoft.com/en-us/library/7y0f9tby.aspx  
  
P.S: I do not know if it needs to be wrapped with `__cplusplus < 201103L` check

---

## Comment 4

> Username: pdimov  
> Created at: 2017-11-27 20:14:09 UTC  
> Url: https://github.com/boostorg/winapi/issues/62#issuecomment-347313011  

> Naming the structs will change the syntax to access the members, so it will be a breaking change.  
  
What is the syntax that is used to access these members today? The following fails for me:  
  
```  
#include <boost/winapi/system.hpp>  
  
int main()  
{  
    boost::winapi::SYSTEM_INFO_ si = {};  
    si.wProcessorArchitecture;  
}  
```

---

## Comment 5

> Username: Lastique  
> Created at: 2017-11-27 20:18:49 UTC  
> Url: https://github.com/boostorg/winapi/issues/62#issuecomment-347314445  

According to [MSDN](https://msdn.microsoft.com/en-us/library/windows/desktop/ms724958(v=vs.85).aspx) it looks like this is the intended syntax. It may be MSVC-specific though, I don't know.

---

## Comment 6

> Username: pdimov  
> Created at: 2017-11-27 20:23:28 UTC  
> Url: https://github.com/boostorg/winapi/issues/62#issuecomment-347315910  

What does MSDN have to do with it? I'm asking what is the syntax that is currently supported by Winapi and that will supposedly be broken by naming the `struct`. The above fails on MSVC, so it's not that.

---

## Comment 7

> Username: Lastique  
> Created at: 2017-11-27 20:36:09 UTC  
> Url: https://github.com/boostorg/winapi/issues/62#issuecomment-347319761  

The intended syntax is the same as for `SYSTEM_INFO` from Windows SDK. MSDN article shows that `wProcessorArchitecture` should be directly accessible as if a member of `SYSTEM_INFO`. It does work like that on my end with the real `SYSTEM_INFO` but not with the one from Boost.WinAPI. This is a bug, which I was not aware of before.

---

## Comment 8

> Username: Lastique  
> Created at: 2017-11-27 20:45:26 UTC  
> Url: https://github.com/boostorg/winapi/issues/62#issuecomment-347322175  

Strangely enough, the structure is declared the same way in Windows SDK 10 and it works there somehow.

---

## Comment 9

> Username: pdimov  
> Created at: 2017-11-27 20:52:34 UTC  
> Url: https://github.com/boostorg/winapi/issues/62#issuecomment-347324157  

`DUMMYSTRUCTNAME` and `DUMMYUNIONNAME` in the `<windows.h>` definition are macros that expand to nothing (when nameless structs are supported), which is why the intended syntax works. (The macros expand to `s` and `u` otherwise.)  
  
Winapi however uses `DUMMYSTRUCTNAME` literally, it's not a macro. So the syntax to access the struct member would be  
  
```  
#include <boost/winapi/system.hpp>  
  
int main()  
{  
    boost::winapi::SYSTEM_INFO_ si = {};  
    si.DUMMYUNIONNAME.DUMMYSTRUCTNAME.wProcessorArchitecture;  
}  
```  
  
which I doubt anyone ever used. :-)  
  
You are however correct, adding a name to the struct itself (rather than the member) does break the syntax, so we can't fix the warning by changing the definition of `SYSTEM_INFO_`.

---

## Comment 10

> Username: Lastique  
> Created at: 2017-11-27 21:00:22 UTC  
> Url: https://github.com/boostorg/winapi/issues/62#issuecomment-347326302  

Ah, those lovely macros in Windows SDK.  
  
Should be fixed in https://github.com/boostorg/winapi/commit/f004b4753d6fee6f68c90cfc65b075fa0a33d092. Hopefully, this should also silence the warnings.  
  
Thanks for alerting me about the syntax bug.

---

## Comment 11

> Username: pdimov  
> Created at: 2017-11-27 21:07:07 UTC  
> Url: https://github.com/boostorg/winapi/issues/62#issuecomment-347328083  

Confirmed warning-free on mingw g++ 7 now. Thanks.
