# #56 Improve CMake Windows support with DLLs [Closed]

> Username: 3Descape  
> Created at: 2021-12-29 20:46:58 UTC  
> Updated at: 2021-12-31 08:53:45 UTC  
> Closed at: 2021-12-31 03:48:50 UTC  
> Url: https://github.com/boostorg/boost_install/pull/56  

This commit sets the IMPORTED_LOCATION property of the imported target to the .dll.  
This allows developers to consume the runtime dependend .dlls via the [$<TARGET_RUNTIME_DLLS:tgt>](https://cmake.org/cmake/help/v3.22/manual/cmake-generator-expressions.7.html#genex:TARGET_RUNTIME_DLLS)  
generator expression on windows.  
  
An open question is, wheter or not we should set the [IMPORTED_LOCATION ](https://cmake.org/cmake/help/latest/prop_tgt/IMPORTED_LOCATION.html)for all platforms, not only windows, as this doesn't seem to have negative side-effects. I am no CMake nor b2/jam expert though, so I'd be happy about some feedback.   
  
Signed-off-by: Michael Lackner <mlackner@student.tugraz.at>

---

## Comment 1

> Username: pdimov  
> Created_at: 2021-12-30 00:26:12 UTC  
> Url: https://github.com/boostorg/boost_install/pull/56#issuecomment-1002823807  

I suspect that this isn't going to work correctly for MinGW (where import libraries are called `foo.dll.a`, or were they `libfoo.dll.a`) or  Cygwin, which had its own convention, but I'm not quite sure what's the right `b2` way to get the actual DLL name.

---

## Comment 2

> Username: grisumbras  
> Created_at: 2021-12-30 13:28:40 UTC  
> Url: https://github.com/boostorg/boost_install/pull/56#issuecomment-1003029514  

These can be used to get proper suffixes and prefixes:  
https://github.com/boostorg/build/blob/develop/src/build/type.jam#L275  
https://github.com/boostorg/build/blob/develop/src/build/type.jam#L305  
  
The target types are defined here:  
https://github.com/boostorg/build/blob/develop/src/tools/types/lib.jam  
  
Something like this will probably work  
```  
local prefix = [ type.generated-target-prefix IMPORT_LIB : $(ps) ] ;  
local suffix = [ type.generated-target-suffix IMPORT_LIB : $(ps) ] ;  
local dllname = "$(prefix)$(fname:BS=$(suffix))" ;  
```

---

## Comment 3

> Username: 3Descape  
> Created_at: 2021-12-30 18:16:55 UTC  
> Url: https://github.com/boostorg/boost_install/pull/56#issuecomment-1003134318  

Thank you very much for the replies!   
@grisumbras These were some great ressources!  
  
I did try out your solution and adapted it(currently for MSCV only) to a functional equivalent solution to my previous commit.   
I used   
```  
local dllname = [ sequence.join $(prefix) $(fname:BS=) "." $(suffix) ] ;  
```  
, as `$(prefix)` and `$(suffix)` can be undefined and it seems like the [variable exansion](https://www.boost.org/doc/libs/1_78_0/tools/build/doc/html/index.html#jam.language.variables.expansion) then causes the `dllname` to be empty as well.   
  
Furthermore we need the `SHARED_LIB` type to get the `.dll` suffix and prefix.   
  
I will try to improve the implementation to the best of my abilities within the next few day to make it work for multiple toolchains/platforms.   
  
Thanks for the support ;)

---

## Comment 4

> Username: pdimov  
> Created_at: 2021-12-30 18:24:16 UTC  
> Url: https://github.com/boostorg/boost_install/pull/56#issuecomment-1003136648  

I don't think this is going to work correctly either, because `fname` already has a prefix and a suffix, appropriate for its type, and those need to be taken into account.

---

## Comment 5

> Username: pdimov  
> Created_at: 2021-12-31 03:48:50 UTC  
> Url: https://github.com/boostorg/boost_install/pull/56#issuecomment-1003257711  

Implemented in https://github.com/boostorg/boost_install/commit/f23b121b3da409ff6b2b959ee4ce716812b29ad9. Let me know if something's not right.

---

## Comment 6

> Username: 3Descape  
> Created_at: 2021-12-31 08:53:44 UTC  
> Url: https://github.com/boostorg/boost_install/pull/56#issuecomment-1003312726  

Looking good :)   
Thank you very much!   
  
Have a nice weekend and a happy new year.

---
