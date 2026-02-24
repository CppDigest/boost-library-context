# #83 - clang-cl compilation error [Closed]

> Username: egorpugin  
> Created at: 2020-06-02 20:09:50 UTC  
> Updated at: 2020-06-03 20:26:58 UTC  
> Closed at: 2020-06-03 20:26:58 UTC  
> Url: https://github.com/boostorg/winapi/issues/83  

Clang-cl from master.  
Removing `::` fixes errors.  
  
```  
[sw.client.manager-0.4.2]/src/sw/manager/settings.cpp  
In file included from D:/dev/cppan2/client2/src/sw/manager/settings.cpp:20:  
In file included from D:/dev/primitives/src/log/include\primitives/log.h:13:  
In file included from D:/dev/swst/pkg/8d/ed/6a12/src/sdir/include\boost/log/trivial.hpp:23:  
In file included from D:/dev/swst/pkg/8d/ed/6a12/src/sdir/include\boost/log/sources/severity_logger.hpp:20:  
In file included from D:/dev/swst/pkg/8d/ed/6a12/src/sdir/include\boost/log/detail/light_rw_mutex.hpp:39:  
D:/dev/swst/pkg/3c/51/ca71/src/sdir/include\boost/winapi/srw_lock.hpp(73,43): error: no type named '_RTL_SRWLOCK' in the global namespace; did you mean simply '_RTL_SRWLOCK'?  
    ::InitializeSRWLock(reinterpret_cast< ::_RTL_SRWLOCK* >(SRWLock));  
                                          ^~~~~~~~~~~~~~  
                                          _RTL_SRWLOCK  
D:/dev/swst/pkg/3c/51/ca71/src/sdir/include\boost/winapi/srw_lock.hpp(61,32): note: '_RTL_SRWLOCK' declared here  
typedef struct BOOST_MAY_ALIAS _RTL_SRWLOCK {  
                               ^  
D:/dev/swst/pkg/3c/51/ca71/src/sdir/include\boost/winapi/srw_lock.hpp(73,5): error: no member named 'InitializeSRWLock' in the global namespace; did you mean simply 'InitializeSRWLock'?  
    ::InitializeSRWLock(reinterpret_cast< ::_RTL_SRWLOCK* >(SRWLock));  
    ^~~~~~~~~~~~~~~~~~~  
    InitializeSRWLock  
D:/dev/swst/pkg/3c/51/ca71/src/sdir/include\boost/winapi/srw_lock.hpp(71,25): note: 'InitializeSRWLock' declared here  
BOOST_FORCEINLINE VOID_ InitializeSRWLock(PSRWLOCK_ SRWLock)  
                        ^  
D:/dev/swst/pkg/3c/51/ca71/src/sdir/include\boost/winapi/srw_lock.hpp(78,49): error: no type named '_RTL_SRWLOCK' in the global namespace; did you mean simply '_RTL_SRWLOCK'?  
    ::ReleaseSRWLockExclusive(reinterpret_cast< ::_RTL_SRWLOCK* >(SRWLock));  
                                                ^~~~~~~~~~~~~~  
                                                _RTL_SRWLOCK  
D:/dev/swst/pkg/3c/51/ca71/src/sdir/include\boost/winapi/srw_lock.hpp(61,32): note: '_RTL_SRWLOCK' declared here  
typedef struct BOOST_MAY_ALIAS _RTL_SRWLOCK {  
                               ^  
D:/dev/swst/pkg/3c/51/ca71/src/sdir/include\boost/winapi/srw_lock.hpp(78,5): error: no member named 'ReleaseSRWLockExclusive' in the global namespace; did you mean simply  
      'ReleaseSRWLockExclusive'?  
    ::ReleaseSRWLockExclusive(reinterpret_cast< ::_RTL_SRWLOCK* >(SRWLock));  
    ^~~~~~~~~~~~~~~~~~~~~~~~~  
    ReleaseSRWLockExclusive  
D:/dev/swst/pkg/3c/51/ca71/src/sdir/include\boost/winapi/srw_lock.hpp(76,25): note: 'ReleaseSRWLockExclusive' declared here  
BOOST_FORCEINLINE VOID_ ReleaseSRWLockExclusive(PSRWLOCK_ SRWLock)  
                        ^  
... output stripped ...  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2020-06-02 20:39:27 UTC  
> Url: https://github.com/boostorg/winapi/issues/83#issuecomment-637793294  

Removing `::` is not the correct solution since we are casting to `_RTL_SRWLOCK` from Windows SDK, not from Boost.WinAPI. Same with functions.  
  
I suspect, there's something odd with your Windows SDK if it indicates that it supports Windows Vista but doesn't define `_RTL_SRWLOCK` and related API. What Windows SDK are you using and does it define `_RTL_SRWLOCK` and under what conditions?

---

## Comment 2

> Username: egorpugin  
> Created at: 2020-06-02 20:57:57 UTC  
> Updated at: 2020-06-02 20:59:26 UTC  
> Url: https://github.com/boostorg/winapi/issues/83#issuecomment-637802455  

> Removing :: is not the correct  
  
That was a quick note.  
  
> What Windows SDK are you using and does it define _RTL_SRWLOCK and under what conditions?  
  
I'm on the latest sdk - 10.0.19041. I think it's in global ns.  
  
Upd.:  
Not sure, maybe just clang-cl issue? Since it's really from master.

---

## Comment 3

> Username: Lastique  
> Created at: 2020-06-02 22:08:10 UTC  
> Url: https://github.com/boostorg/winapi/issues/83#issuecomment-637831828  

10.0.19041 is a pre-release version, the latest release is 10.0.18362. I'm not seeing any test failures with clang-cl 10.0 from VS 2019 and Windows SDK 10.0.18362.  
  
Can you try with the released clang-cl and Windows SDK?

---

## Comment 4

> Username: egorpugin  
> Created at: 2020-06-03 09:31:39 UTC  
> Url: https://github.com/boostorg/winapi/issues/83#issuecomment-638080440  

10.0.19041 is offered from VS installer, since win10 2004 is released.  
Not sure if it's pre-release version.  
I'll try with different compiler and sdk and report back.

---

## Comment 5

> Username: egorpugin  
> Created at: 2020-06-03 17:38:19 UTC  
> Url: https://github.com/boostorg/winapi/issues/83#issuecomment-638347442  

clang-cl 9.0 + winsdk 10.0.18362.0 gives same error.  
Maybe some boost header skipped or msvc/clang def is not working to do proper include order?  
  
Command line + output (clang-cl 9.0 + winsdk 10.0.18362.0).  
https://pastebin.com/iHu4ihnF

---

## Comment 6

> Username: Lastique  
> Created at: 2020-06-03 19:00:02 UTC  
> Updated at: 2020-06-03 19:02:36 UTC  
> Url: https://github.com/boostorg/winapi/issues/83#issuecomment-638397678  

In that log, I can see you're using clang 11, at least its headers are among the included ones.  
  
I can see `BOOST_USE_WINDOWS_H` is defined, and I can see `boost/winapi/basic_types.hpp` gets included at some point, but I don't see `windows.h` being included (which it [should](https://github.com/boostorg/winapi/blob/e16ec9e2a0874a6bcb12b427728bbaee4527cdfc/include/boost/winapi/basic_types.hpp#L19)). Which makes me think this is either a compiler issue, or some sort of a preprocessor bug, or your Boost source was modified somehow.  
  
Can you prepare a small reproducer test?

---

## Comment 7

> Username: egorpugin  
> Created at: 2020-06-03 19:07:39 UTC  
> Url: https://github.com/boostorg/winapi/issues/83#issuecomment-638401876  

I did not change clang headers, just left them from clang-11.  
I'll try to minify.

---

## Comment 8

> Username: Lastique  
> Created at: 2020-06-03 19:10:10 UTC  
> Url: https://github.com/boostorg/winapi/issues/83#issuecomment-638403330  

Can you reproduce with *vanilla released versions* of components? No pre-released stuff, no leftover headers etc. please.

---

## Comment 9

> Username: egorpugin  
> Created at: 2020-06-03 19:11:58 UTC  
> Url: https://github.com/boostorg/winapi/issues/83#issuecomment-638404321  

Switched clang headers, same result. (Minifying.)

---

## Comment 10

> Username: egorpugin  
> Created at: 2020-06-03 19:16:52 UTC  
> Url: https://github.com/boostorg/winapi/issues/83#issuecomment-638406877  

Looks like I have such code to create error with clang cl (though msvc works).  
  
```  
#ifdef BOOST_USE_WINDOWS_H  
#undef BOOST_USE_WINDOWS_H  
#define REDEFINE_BOOST_USE_WINDOWS_H  
#endif  
  
#include <boost/stacktrace.hpp>  
  
// remove following 3 lines to fix the issue with clang-cl  
#ifdef REDEFINE_BOOST_USE_WINDOWS_H  
#define BOOST_USE_WINDOWS_H  
#endif  
  
#include <boost/log/trivial.hpp>  
```

---

## Comment 11

> Username: egorpugin  
> Created at: 2020-06-03 19:22:56 UTC  
> Updated at: 2020-06-03 19:23:52 UTC  
> Url: https://github.com/boostorg/winapi/issues/83#issuecomment-638409980  

Do not remember why I have such strange defs, maybe wrap older stacktrace versions. I use the latest boost.  
Removed them, works both on msvc/clang-cl.  
  
Upd.: feel free to close, if you consider this not a bug.

---

## Comment 12

> Username: Lastique  
> Created at: 2020-06-03 20:26:58 UTC  
> Url: https://github.com/boostorg/winapi/issues/83#issuecomment-638443199  

Ok, I see. Thanks for investigating.
