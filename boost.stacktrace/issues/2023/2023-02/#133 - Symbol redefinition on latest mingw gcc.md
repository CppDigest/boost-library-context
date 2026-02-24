# #133 - Symbol redefinition on latest mingw gcc [Closed]

> Username: guusw  
> Created at: 2023-02-09 05:22:56 UTC  
> Updated at: 2024-02-19 08:34:03 UTC  
> Closed at: 2024-02-19 08:34:03 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/133  

Using msys2, updating to the latest gcc-mingw(x64), stacktrace doesn't seem to build anymore, the UUIDs of IDebugClient, etc. seem to be already defined in dbgeng.h   
  
```  
D:/a/shards/shards/build/_deps/boost-src/libs/stacktrace/include/boost/stacktrace/detail/frame_msvc.ipp:31:5: error: redefinition of 'struct __mingw_uuidof_s<IDebugClient>'  
   31 |     __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      |     ^~~~~~~~~~~~~~~  
C:/msys64/mingw64/include/dbgeng.h:262:1: note: previous definition of 'struct __mingw_uuidof_s<IDebugClient>'  
  262 | __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      | ^~~~~~~~~~~~~~~  
D:/a/shards/shards/build/_deps/boost-src/libs/stacktrace/include/boost/stacktrace/detail/frame_msvc.ipp:31:5: error: redefinition of 'constexpr const GUID& __mingw_uuidof() [with T = IDebugClient; GUID = GUID]'  
   31 |     __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      |     ^~~~~~~~~~~~~~~  
C:/msys64/mingw64/include/dbgeng.h:262:1: note: 'constexpr const GUID& __mingw_uuidof() [with T = IDebugClient; GUID = GUID]' previously declared here  
  262 | __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      | ^~~~~~~~~~~~~~~  
```  
  
```  
> gcc --version  
gcc.exe (Rev10, Built by MSYS2 project) 12.2.0  
> uname -a  
MSYS_NT-10.0-22000 Fragcolor-Eim 3.4.5.x86_64 2023-01-20 09:17 UTC x86_64 Msys  
```

---

## Comment 1

> Username: yh-sb  
> Created at: 2023-05-20 18:21:55 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/133#issuecomment-1555966377  

Bump from me.  
  
Same build issue with MinGW-w64 GCC 13.1 `gcc.exe (MinGW-W64 x86_64-ucrt-mcf-seh, built by Brecht Sanders) 13.1.0` from here: https://winlibs.com/

---

## Comment 2

> Username: petrutlucian94  
> Created at: 2023-08-18 12:09:49 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/133#issuecomment-1683826365  

Those definitions were recently added to the mingw ``dbgeng.h`` header, causing the redefinition error: https://github.com/mirror/mingw-w64/commit/ce5a9f624dfc691082dad2ea2af7b1985e3476b5

---

## Comment 3

> Username: yh-sb  
> Created at: 2023-08-21 21:30:23 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/133#issuecomment-1687071200  

Pull request to fix this issue: https://github.com/boostorg/stacktrace/pull/140

---

## Comment 4

> Username: valydumitru01  
> Created at: 2023-10-11 19:19:59 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/133#issuecomment-1758381004  

Not only I get this error, but also I get another error when I try to use default stack traces (boost_stacktrace_basic) the memory addresses are undecodable. If you try to use addr2line you get ?? all the time. Making printing stack traces an impossible task for MinGW.  
My solution was downgrading to MinGW 7.5.

---

## Comment 5

> Username: valydumitru01  
> Created at: 2023-10-11 19:20:45 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/133#issuecomment-1758382061  

Also it did not work using backtrace either.

---

## Comment 6

> Username: apolukhin  
> Created at: 2024-02-19 08:34:03 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/133#issuecomment-1951942303  

Fixed in https://github.com/boostorg/stacktrace/pull/140  
  
If the fix does not help, please create a PR with a nee fix (I have no ready-to-use environments with the required version of MinGW, so it's quite complocated to find the right fix)
