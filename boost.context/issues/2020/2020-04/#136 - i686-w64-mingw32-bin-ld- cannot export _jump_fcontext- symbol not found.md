# #136 - [1.73.0] i686-w64-mingw32/bin/ld: cannot export _jump_fcontext: symbol not found [Closed]

> Username: jschueller  
> Created at: 2020-04-16 12:28:29 UTC  
> Updated at: 2020-08-20 14:38:12 UTC  
> Closed at: 2020-08-20 14:38:12 UTC  
> Url: https://github.com/boostorg/context/issues/136  

the 1.73 version fails to build on the 32 bits variant (but succeeds on 64bits) of mingw with the following error:  
```  
gcc.link.dll bin.v2/libs/context/build/gcc-mingw64/release/binary-format-pe/target-os-windows/threading-multi/visibility-hidden/libboost_context.dll.a  
  
    "i686-w64-mingw32-g++"   "-Wl,--out-implib,bin.v2/libs/context/build/gcc-mingw64/release/binary-format-pe/target-os-windows/threading-multi/visibility-hidden/libboost_context.dll.a" -o "bin.v2/libs/context/build/gcc-mingw64/release/binary-format-pe/target-os-windows/threading-multi/visibility-hidden/libboost_context.dll" -Wl,-h -Wl,libboost_context.dll -shared -Wl,--start-group "bin.v2/libs/context/build/gcc-mingw64/release/binary-format-pe/target-os-windows/threading-multi/visibility-hidden/asm/make_i386_ms_pe_gas.o" "bin.v2/libs/context/build/gcc-mingw64/release/binary-format-pe/target-os-windows/threading-multi/visibility-hidden/asm/jump_i386_ms_pe_gas.o" "bin.v2/libs/context/build/gcc-mingw64/release/binary-format-pe/target-os-windows/threading-multi/visibility-hidden/asm/ontop_i386_ms_pe_gas.o" "bin.v2/libs/context/build/gcc-mingw64/release/binary-format-pe/target-os-windows/threading-multi/visibility-hidden/dummy.o" "bin.v2/libs/context/build/gcc-mingw64/release/binary-format-pe/target-os-windows/threading-multi/visibility-hidden/windows/stack_traits.o"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -m32 -mthreads -fvisibility=hidden -fvisibility-inlines-hidden -march=i686 -Wl,-O1,--sort-common,--as-needed -fstack-protector  
  
/usr/lib/gcc/i686-w64-mingw32/9.3.0/../../../../i686-w64-mingw32/bin/ld: cannot export _jump_fcontext: symbol not found  
```  
  
The faulty commit is https://github.com/boostorg/context/commit/85783e8cbba03804e13b3d314112df6b089ed2c0 from #127  
Maybe fixing for LLVM's ld resulted in breaking the build with GNU's ld.  
  
cc @ericastor

---

## Comment 1

> Username: mabrarov  
> Created at: 2020-04-28 14:02:20 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-620627369  

I have the same issue with the latest **release** version of Boost - 1.73.0 - and MinGW 7.3.0 32 bits on Windows Server 2019 (mcr.microsoft.com/windows/servercore:ltsc2019 docker image):  
  
```  
gcc.link.dll bin.v2\libs\context\build\gcc-7.3.0\debug\threading-multi\visibility-hidden\libboost_context-mgw7-mt-d-x32-1_73.dll.a  
C:/mingw32/bin/../lib/gcc/i686-w64-mingw32/7.3.0/../../../../i686-w64-mingw32/bin/ld.exe: Cannot export _jump_fcontext: symbol not found  
C:/mingw32/bin/../lib/gcc/i686-w64-mingw32/7.3.0/../../../../i686-w64-mingw32/bin/ld.exe: Cannot export _make_fcontext: symbol not found  
C:/mingw32/bin/../lib/gcc/i686-w64-mingw32/7.3.0/../../../../i686-w64-mingw32/bin/ld.exe: Cannot export _ontop_fcontext: symbol not found  
collect2.exe: error: ld returned 1 exit status  
```

---

## Comment 2

> Username: ericastor  
> Created at: 2020-04-28 14:54:34 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-620658224  

It sounds like the issue here is that in 32-bit MinGW, the GNU assembler is prepending a `_` onto the symbols defined in assembler files - which is why they were getting exported correctly before - and this isn't the case with LLVM's assembler. One of GNU and LLVM has a bug here. Since there's no actual reference for GNU-style assembly for Windows, the question is: which is a better idea?  
  
Personally, I think silently changing the name of a symbol is bad behavior for an assembler, which is supposed to do exactly as they're told. Maybe this should be filed as a bug against GNU's as?  
  
In the meantime... it seems like a shame, but do we need to fork these assembly files into GNU vs. LLVM to keep this working?

---

## Comment 3

> Username: mabrarov  
> Created at: 2020-04-28 15:20:11 UTC  
> Updated at: 2020-04-28 15:20:57 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-620673222  

Regarding shame - I checked [1.73.0 release notes](https://www.boost.org/users/history/version_1_73_0.html):  
  
> Compilers Tested  
>   
> Boost's primary test compilers are:   
> ...  
> Windows:  
> ...  
> * GCC, C++17: 7.1.0, 7.2.0, **7.3.0**  
  
and I became curios what combination of build flags / platforms was tested and how was it tested? Maybe I just miss some build (Boost.Build) flag which makes MinGW 7.3.0 exporting symbols on 32-bit platform correctly?

---

## Comment 4

> Username: jschueller  
> Created at: 2020-04-28 16:14:02 UTC  
> Updated at: 2020-04-28 16:14:12 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-620706398  

Maybe they just test on x86_64

---

## Comment 5

> Username: jschueller  
> Created at: 2020-04-28 16:16:03 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-620707582  

@ericastor  I think the gnu assembler was there first ;)

---

## Comment 6

> Username: ericastor  
> Created at: 2020-04-28 16:17:56 UTC  
> Updated at: 2020-04-28 16:19:32 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-620708746  

@jschueller Doesn't mean it can't have bugs... ;)  
  
EDIT: Also, please note that I contributed the fix, but don't know Boost project policies etc. - I'm just a driveby contributor. Maybe someone with more experience here can chime in?

---

## Comment 7

> Username: mabrarov  
> Created at: 2020-04-28 21:03:28 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-620853626  

I found that Boost 1.73.0 libraries built with MinGW 7.3.0 have mgw7 suffix instead of mgw73 suffix comparing to Boost 1.72.0, e.g. libboost_atomic-**mgw7**-mt-d-x64-1_73.a instead of libboost_atomic-**mgw73**-mt-d-x64-1_72.a. This difference breaks FindBoost module of CMake 3.17.2 and it cannot find built Boost 1.73.0 libraries.

---

## Comment 8

> Username: mabrarov  
> Created at: 2020-08-19 19:17:24 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-676611699  

Still an issue with MinGW 7.3.0 x86 build of Boost 1.74.0

---

## Comment 9

> Username: ericastor  
> Created at: 2020-08-19 19:32:10 UTC  
> Updated at: 2020-08-19 19:33:46 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-676618555  

Apologies; I'm still going to suggest that this should be filed as a bug against either gas or LLVM/clang. It seems likely this should at least be a configurable behavior.  
  
Alternatively, someone could fork the assembly files into a MinGW and non-MinGW version.  
  
EDIT: @mabrarov - your specific problem sounds unrelated to this bug? Maybe open another issue?

---

## Comment 10

> Username: mati865  
> Created at: 2020-08-19 20:12:01 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-676639397  

Both Binutils and LLVM in MinGW mode will prepend `_` to 32-bit symbols. This is sort of a "convention" for that target.

---

## Comment 11

> Username: jschueller  
> Created at: 2020-08-20 06:36:43 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-677303216  

so llvm would be faulty here ?

---

## Comment 12

> Username: mati865  
> Created at: 2020-08-20 06:44:00 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-677331373  

No, LLVM should work fine without https://github.com/boostorg/context/pull/127 in MinGW mode (calling via `ld.lld` or `lld-link` with `-lldmingw`).  
I think https://github.com/boostorg/context/pull/127 might even break LLD in MinGW mode.

---

## Comment 13

> Username: jschueller  
> Created at: 2020-08-20 06:51:40 UTC  
> Updated at: 2020-08-20 06:52:37 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-677359999  

the question would be how to differentiate regular lld from lld in mingw mode / mingw-binutils if splitting the assembly files is in order

---

## Comment 14

> Username: mati865  
> Created at: 2020-08-20 06:56:33 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-677368467  

Those files appear to be meant for MinGW only, so using them without MinGW linker sounds like asking for troubles already.

---

## Comment 15

> Username: jschueller  
> Created at: 2020-08-20 07:03:16 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-677379354  

so we would need a new jump_i386_ms_pe_lld.asm ?

---

## Comment 16

> Username: mati865  
> Created at: 2020-08-20 07:08:42 UTC  
> Updated at: 2020-08-20 07:09:56 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-677388143  

I don't think so, #127 looks to me as user error (using wrong linker).  
MSVC and MinGW have different ABIs so one has to always use correct linker. The only way to mix them is through DLLs, otherwise it's asking for problems.

---

## Comment 17

> Username: jschueller  
> Created at: 2020-08-20 07:13:15 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-677396493  

so it should have been masm, not gas ?

---

## Comment 18

> Username: ericastor  
> Created at: 2020-08-20 13:02:19 UTC  
> Updated at: 2020-08-20 13:04:02 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-677650756  

Hm. This has actually gotten interesting... because I can say from quite a bit of experience that this was actually *working*. Removing this is breaking a consuming piece of software.  
  
Please keep in mind that clang is capable of targeting Windows, and can incorporate GNU assembler files into its builds. If you configure it in this way, this currently works. I think it might be worth having support for both worlds, even if we need to distinguish them with a define.

---

## Comment 19

> Username: mati865  
> Created at: 2020-08-20 13:40:39 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-677671282  

@ericastor GCC/Binutils supports only MinGW mode but Clang/LLVM do support both MinGW and MSVC modes.  
#127 broke MinGW mode so it was reverted by various parties:  
- https://github.com/msys2/MINGW-packages/pull/6504/files#diff-031b04b8132e93b1279e644ca2efadcf  
- https://aur.archlinux.org/cgit/aur.git/tree/PKGBUILD?h=mingw-w64-boost&id=b06f6e147ecd0415999742395abbd3d34c88a01f#n31  
- https://src.fedoraproject.org/rpms/mingw-boost/blob/c40a70c9b75fba763e8bb288f03f8664d0d64ea3/f/boost-mingw.patch#_107  
  
Looking by the name `*_i386_ms_pe_gas.asm` sound like they are MinGW mode for GNU assembler and `*_i386_ms_pe_masm.asm` are meant for Microsoft's assembler.

---

## Comment 20

> Username: ericastor  
> Created at: 2020-08-20 13:57:13 UTC  
> Updated at: 2020-08-20 13:57:50 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-677680879  

@mati865 Fair enough - but I do have a legitimate use case. We compile with Clang targeting MSVC mode - and do not use Microsoft's assembler.  
  
I'm currently working on getting MASM support into Clang, but it's not there yet. However, Clang has been capable for some time of using GNU-style assembler to target MSVC. As mentioned, this works quite well. Can we consider using a define from the build files to distinguish these cases instead?

---

## Comment 21

> Username: mati865  
> Created at: 2020-08-20 14:20:30 UTC  
> Url: https://github.com/boostorg/context/issues/136#issuecomment-677695699  

@ericastor sounds fair. In that case leaving `*gas` for MinGW and creating new files for Clang targeting MSVC sounds like logical thing to do.
