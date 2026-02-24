# #82 - Compile error when including boost\thread\win32\thread_data.hpp on win64 [Closed]

> Username: vlj  
> Created at: 2020-02-13 14:06:40 UTC  
> Updated at: 2020-02-13 16:38:37 UTC  
> Closed at: 2020-02-13 16:38:37 UTC  
> Url: https://github.com/boostorg/winapi/issues/82  

Hi,  
  
when I try to include boost\thread\win32\thread_data.hpp  in a x64 project, I get compilation error:  
  
> include\boost/thread/win32/thread_data.hpp(150,1): error C2665: '_InterlockedIncrement': none of the 4 overloads could convert all the argument types  
>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winbase.h(9334,1): message : could be 'unsigned __int64 _InterlockedIncrement(volatile unsigned __int64 *)'  
> C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winbase.h(9322,1): message : or       'unsigned long _InterlockedIncrement(volatile unsigned long *)'  
>C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\um\winbase.h(9313,1): message : or       'unsigned int _InterlockedIncrement(volatile unsigned int *)'  
>C:\Program Files (x86)\Microsoft Visual Studio\2019\Professional\VC\Tools\MSVC\14.24.28314\include\intrin0.h(195,1): message : or       'long _InterlockedIncrement(volatile long *)'  
>include\boost/thread/win32/thread_data.hpp(150,1): message : while trying to match the argument list '(int *)'  
  
I think the issue is that the BOOST_INTERLOCKED_LONG32 in interlocked.hpp introduced in commit https://github.com/boostorg/winapi/commit/65b9c99a6772add56a24272d0b76b611fd92caf4 is not (always) correct.  
There is no  "_InterlockedIncrement(int*)" in any of the header, the closest I found is "_InterlockedIncrement(unsigned int*)" so maybe BOOST_INTERLOCKED_LONG32 can be defined as unsigned int instead of int ?

---

## Comment 1

> Username: Lastique  
> Created at: 2020-02-13 14:32:28 UTC  
> Url: https://github.com/boostorg/winapi/issues/82#issuecomment-585783963  

What is your compiler and command line options?  
  
The macro expands to `int` only when `__LP64__` is defined, which is an indicator of an [LP64](https://en.wikipedia.org/wiki/64-bit_computing#64-bit_data_models) platform, which is *not* Windows. Otherwise it is `long` and there is an overload that matches it in your error message.

---

## Comment 2

> Username: vlj  
> Created at: 2020-02-13 16:38:37 UTC  
> Url: https://github.com/boostorg/winapi/issues/82#issuecomment-585850884  

hmmm actually there is a define __LP64__ in our codebase which mess up compiler selection, so it's on our end. Sorry for the inconvenience.
