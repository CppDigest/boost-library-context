# #50 - MinGW-w64 undefined reference to __imp_make_fcontext / __imp_jump_fcontext [Closed]

> Username: brechtsanders  
> Created at: 2020-05-11 09:33:23 UTC  
> Updated at: 2023-02-14 11:34:24 UTC  
> Closed at: 2021-11-15 17:06:52 UTC  
> Url: https://github.com/boostorg/coroutine/issues/50  

I keep getting undefined refence errors from the linker while the symbols do exist in `.asm` files.  
For example `_make_fcontext` is defined in `libs/context/src/asm/make_i386_ms_pe_gas.asm` and `libs/context/src/asm/make_x86_64_ms_pe_gas.asm`.  
However I have a suspicion the linker is looking for `make_fcontext` (without leading underscore).  
  
Boost version: 1.73.0, but also have this problem with older versions  
OS: Windows 10  
Shell: MSYS2  
Compiler toolset: MinGW-w64 GCC for Windows from http://winlibs.com/ (different versions, e.g. GCC 9.3.0)  
Build command (+ the same with `address-model=32`):  
```  
BOOST_ROOT=$(pwd) MINGW=/ tools/build/src/engine/bjam.exe -q --prefix=/usr/local --build-dir=build_win --build-type=complete --layout=tagged --enable-icu --disable-filesystem2 toolset=gcc target-os=windows abi=ms link=static,shared runtime-link=static,shared variant=release threading=single,multi threadapi=win32 context-impl=winfib address-model=64 --without-python --without-mpi release stage cxxflags="-fpermissive -DBOOST_LOG_WITHOUT_SYSLOG"  
```  
  
```  
gcc.link.dll build_win\boost\bin.v2\libs\coroutine\build\gcc-10.1.0\release\context-impl-winfib\threadapi-win32\visibility-hidden\libboost_coroutine-x64.dll.a  
d:/prog/winlibs64-10.1.0/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/10.1.0/../../../../x86_64-w64-mingw32/bin/ld.exe: build_win\boost\bin.v2\libs\coroutine\build\gcc-10.1.0\release\context-impl-winfib\threadapi-win32\visibility-hidden\detail\coroutine_context.o:coroutine_context.cpp:(.text+0x4f): undefined reference to  
`__imp_make_fcontext'  
d:/prog/winlibs64-10.1.0/mingw64/bin/../lib/gcc/x86_64-w64-mingw32/10.1.0/../../../../x86_64-w64-mingw32/bin/ld.exe: build_win\boost\bin.v2\libs\coroutine\build\gcc-10.1.0\release\context-impl-winfib\threadapi-win32\visibility-hidden\detail\coroutine_context.o:coroutine_context.cpp:(.text+0xd7): undefined reference to  
`__imp_jump_fcontext'  
```

---

## Comment 1

> Username: brechtsanders  
> Created at: 2020-05-11 11:45:07 UTC  
> Url: https://github.com/boostorg/coroutine/issues/50#issuecomment-626652164  

Just found out it works fine with `context-impl=fcontext` (instead of `context-impl=winfib`) on Windows 64-bit, but on Windows 32-bit I still get:  
```  
gcc.link.dll build_win\boost\bin.v2\libs\context\build\gcc-10.1.0\release\threading-multi\visibility-hidden\libboost_context-mt-x32.dll.a  
d:/prog/winlibs32-10.1.0/mingw32/bin/../lib/gcc/i686-w64-mingw32/10.1.0/../../../../i686-w64-mingw32/bin/ld.exe: cannot export _jump_fcontext: symbol not found  
d:/prog/winlibs32-10.1.0/mingw32/bin/../lib/gcc/i686-w64-mingw32/10.1.0/../../../../i686-w64-mingw32/bin/ld.exe: cannot export _make_fcontext: symbol not found  
d:/prog/winlibs32-10.1.0/mingw32/bin/../lib/gcc/i686-w64-mingw32/10.1.0/../../../../i686-w64-mingw32/bin/ld.exe: cannot export _ontop_fcontext: symbol not found  
```

---

## Comment 2

> Username: brechtsanders  
> Created at: 2020-05-12 11:46:53 UTC  
> Updated at: 2020-05-12 12:35:41 UTC  
> Url: https://github.com/boostorg/coroutine/issues/50#issuecomment-627293234  

I just tried to use jwasm to see what happens.  
If I run:  
```  
sed -i.bak -e "s/^.386/.686/; s/stmxcsr/STMXCSR/; s/ldmxcsr/LDMXCSR/; s/jump_fcontext/_&/" libs/context/src/asm/jump_i386_ms_pe_masm.asm  
jwasm $(if ( echo $RUNPLATFORM | grep -q x86_64 ); then echo "-win64"; else echo "-coff"; fi) -Fobuild_win/boost/bin.v2/libs/context/build/gcc-10.1.0/release/link-static/threading-multi/visibility-hidden/asm/jump_i386_ms_pe_gas.o libs/context/src/asm/jump_i386_ms_pe_masm.asm  
```  
I still get:  
```  
d:/prog/winlibs32-10.1.0/mingw32/bin/../lib/gcc/i686-w64-mingw32/10.1.0/../../../../i686-w64-mingw32/bin/ld.exe: cannot export _jump_fcontext: symbol not found  
```  
  
Running `nm` on the resulting object file gives:  
```  
00000000 d .data  
00000000 t .text  
00000000 T _jump_fcontext  
```  
  
Then I tried again with GNU Assembler:  
```  
as -march=i686+687+sse --32 libs/context/src/asm/jump_i386_ms_pe_gas.asm -o build_win/boost/bin.v2/libs/context/build/gcc-10.1.0/release/link-static/threading-multi/visibility-hidden/asm/jump_i386_ms_pe_gas.o  
```  
  
Running `nm` on the resulting object file gives:  
```  
00000000 b .bss  
00000000 d .data  
00000000 d .drectve  
00000000 t .text  
00000001 A @feat.00  
00000000 T _jump_fcontext  
```  
  
So the symbol `_jump_fcontext` is really there, but the linker can't find it.  
  
So this begs the question: is the linker using the object files from assembler?  
It appears to me that it is given the output:  
```  
gcc.link.dll build_win\boost\bin.v2\libs\context\build\gcc-10.1.0\release\threading-multi\visibility-hidden\libboost_context-mt-x32.dll.a  
d:/prog/winlibs32-10.1.0/mingw32/bin/../lib/gcc/i686-w64-mingw32/10.1.0/../../../../i686-w64-mingw32/bin/ld.exe: cannot export _jump_fcontext: symbol not found  
d:/prog/winlibs32-10.1.0/mingw32/bin/../lib/gcc/i686-w64-mingw32/10.1.0/../../../../i686-w64-mingw32/bin/ld.exe: cannot export _make_fcontext: symbol not found  
d:/prog/winlibs32-10.1.0/mingw32/bin/../lib/gcc/i686-w64-mingw32/10.1.0/../../../../i686-w64-mingw32/bin/ld.exe: cannot export _ontop_fcontext: symbol not found  
collect2.exe: error: ld returned 1 exit status  
  
    "g++"   "-Wl,--out-implib,build_win\boost\bin.v2\libs\context\build\gcc-10.1.0\release\threading-multi\visibility-hidden\libboost_context-mt-x32.dll.a" -o "build_win\boost\bin.v2\libs\context\build\gcc-10.1.0\release\threading-multi\visibility-hidden\libboost_context-mt-x32.dll" -Wl,-h -Wl,libboost_context-mt-x32.dll -shared -Wl,--start-group "build_win\boost\bin.v2\libs\context\build\gcc-10.1.0\release\threading-multi\visibility-hidden\asm\make_i386_ms_pe_gas.o" "build_win\boost\bin.v2\libs\context\build\gcc-10.1.0\release\threading-multi\visibility-hidden\asm\jump_i386_ms_pe_gas.o" "build_win\boost\bin.v2\libs\context\build\gcc-10.1.0\release\threading-multi\visibility-hidden\asm\ontop_i386_ms_pe_gas.o" "build_win\boost\bin.v2\libs\context\build\gcc-10.1.0\release\threading-multi\visibility-hidden\dummy.o" "build_win\boost\bin.v2\libs\context\build\gcc-10.1.0\release\threading-multi\visibility-hidden\windows\stack_traits.o"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -m32 -mthreads -fvisibility=hidden -fvisibility-inlines-hidden -march=i686  
```  
  
So why is the linker not finding the symbols from the assembler objects?

---

## Comment 3

> Username: brechtsanders  
> Created at: 2020-05-12 17:35:40 UTC  
> Updated at: 2020-05-12 18:53:23 UTC  
> Url: https://github.com/boostorg/coroutine/issues/50#issuecomment-627488121  

After a lot of trial and error I noticed the link error disappears when I remove the leading underscore from the export directive in the `.asm`files, e.g. replacing this:  
```  
.section .drectve  
.ascii " -export:\"_jump_fcontext\""  
```  
with:  
```  
.section .drectve  
.ascii " -export:\"jump_fcontext\""  
```  
  
FYI: I fixed it in the 3 relevant files at once by doing this:  
```  
sed -i.bak -e 's/^\(\.ascii " -export:\\"\)_/\1/' libs/context/src/asm/jump_i386_ms_pe_gas.asm libs/context/src/asm/make_i386_ms_pe_gas.asm libs/context/src/asm/ontop_i386_ms_pe_gas.asm  
```  
  
So maybe the leading underscore should be removed in the `.drectve` section?

---

## Comment 4

> Username: Kojoley  
> Created at: 2021-11-15 16:30:09 UTC  
> Url: https://github.com/boostorg/coroutine/issues/50#issuecomment-969085763  

This was fixed in https://github.com/boostorg/context/pull/149

---

## Comment 5

> Username: olk  
> Created at: 2021-11-15 17:06:52 UTC  
> Url: https://github.com/boostorg/coroutine/issues/50#issuecomment-969120512  

ty

---

## Comment 6

> Username: excitoon  
> Created at: 2023-02-14 11:33:29 UTC  
> Updated at: 2023-02-14 11:34:24 UTC  
> Url: https://github.com/boostorg/coroutine/issues/50#issuecomment-1429586140  

Thanks @brechtsanders !  
  
I'll leave it here for future guys, there is same problem when you link MSVC6 object files with mingw, it seems impossible to specify `__declspec(dllexport)` right in the code.
