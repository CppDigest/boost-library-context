# #134 - Error when compiling boost since MSYS2 update (02/2023) [Closed]

> Username: mathieu-sousou  
> Created at: 2023-02-15 16:37:17 UTC  
> Updated at: 2023-02-21 20:31:18 UTC  
> Closed at: 2023-02-21 20:31:18 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/134  

Since last msys update (near begin of february), boost does not compile anymore.  
  
On windows, from msys shell:  
````  
pacman -S asciidoc autoconf-wrapper autogen automake-wrapper base bison btyacc dos2unix flex gdb gettext-devel git gperf help2man intltool libtool libunrar-devel make man-db mingw-w64-x86_64-clang-tools-extra mingw-w64-x86_64-cmake mingw-w64-x86_64-gcc-ada mingw-w64-x86_64-gcc-fortran mingw-w64-x86_64-gcc-objc mingw-w64-x86_64-gdb-multiarch mingw-w64-x86_64-libgccjit mingw-w64-x86_64-libmangle-git mingw-w64-x86_64-make mingw-w64-x86_64-nasm mingw-w64-x86_64-tools-git mingw-w64-x86_64-winstorecompat-git patchutils pkgconf pkgfile python-pip quilt reflex scons swig texinfo texinfo-tex tree unrar unzip xmlto  
wget https://boostorg.jfrog.io/artifactory/main/release/1.81.0/source/boost_1_81_0.tar.gz  
tar zxf boost_1_81_0.tar.gz  
cd boost_1_81_0  
./bootstrap.sh --prefix=$PWD/install --with-toolset=gcc   --with-libraries=atomic,container,date_time,exception,filesystem,graph,iostreams,log,math,program_options,regex,serialization,stacktrace,system,test,timer,thread  
./b2.exe  
````  
I got this error:  
````  
gcc.compile.c++ bin.v2\libs\stacktrace\build\gcc-12\release\link-static\threading-multi\visibility-hidden\windbg_cached.o  
In file included from C:/msys64/mingw64/include/corecrt.h:10,  
                 from C:/msys64/mingw64/include/crtdefs.h:10,  
                 from C:/msys64/mingw64/include/stddef.h:7,  
                 from C:/msys64/mingw64/lib/gcc/x86_64-w64-mingw32/12.2.0/include/stddef.h:1,  
                 from C:/msys64/mingw64/include/c++/12.2.0/cstddef:50,  
                 from ./boost/config/compiler/gcc.hpp:165,  
                 from ./boost/config.hpp:39,  
                 from ./boost/stacktrace/detail/frame_msvc.ipp:10,  
                 from libs\stacktrace\build\..\src\windbg_cached.cpp:10:  
./boost/stacktrace/detail/frame_msvc.ipp:31:5: error: redefinition of 'struct __mingw_uuidof_s<IDebugClient>'  
   31 |     __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      |     ^~~~~~~~~~~~~~~  
C:/msys64/mingw64/include/dbgeng.h:262:1: note: previous definition of 'struct __mingw_uuidof_s<IDebugClient>'  
  262 | __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      | ^~~~~~~~~~~~~~~  
./boost/stacktrace/detail/frame_msvc.ipp:31:5: error: redefinition of 'constexpr const GUID& __mingw_uuidof() [with T = IDebugClient; GUID = GUID]'  
   31 |     __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      |     ^~~~~~~~~~~~~~~  
C:/msys64/mingw64/include/dbgeng.h:262:1: note: 'constexpr const GUID& __mingw_uuidof() [with T = IDebugClient; GUID = GUID]' previously declared here  
  262 | __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
      | ^~~~~~~~~~~~~~~  
./boost/stacktrace/detail/frame_msvc.ipp:31:5: error: redefinition of 'constexpr const GUID& __mingw_uuidof() [with T = IDebugClient*; GUID = GUID]'  
   31 |     __CRT_UUID_DECL(IDebugClient,0x27fe5639,0x8407,0x4f47,0x83,0x64,0xee,0x11,0x8f,0xb0,0x8a,0xc8)  
````  
  
I can bring more details if needed.

---

## Comment 1

> Username: mathieu-sousou  
> Created at: 2023-02-19 11:25:56 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/134#issuecomment-1435962347  

same as issue #133
