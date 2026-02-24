# #685 - cross compile on linux with mingw fails if midnight commander is installed. [Open]

> Username: chris-stones  
> Created at: 2020-12-16 22:07:37 UTC  
> Updated at: 2021-05-29 16:23:06 UTC  
> Url: https://github.com/boostorg/build/issues/685  

Building on Linux, and targeting windows fails because `compile.mc` picks up `/usr/bin/mc` (midnight commander), and confuses it for [mc.exe](https://docs.microsoft.com/en-us/windows/win32/wes/message-compiler--mc-exe-)    
    
You can work around by renaming the midnight-commander binary.    
Then the compile works fine.    
  
Compiling with conan.    
  
command -   
```bash  
conan install .. --profile=mingw-w64-i686 --build missing  
```  
  
conanfile.txt  
```  
[requires]  
boost/1.74.0  
  
[generators]  
cmake  
```  
  
~/.conan/profiles/mingw-w64-i686  
```  
toolchain=/usr/i686-w64-mingw32 # Adjust this path  
target_host=i686-w64-mingw32  
cc_compiler=gcc  
cxx_compiler=g++  
  
[env]  
CONAN_CMAKE_FIND_ROOT_PATH=$toolchain  # Optional, for CMake to find things in that folder  
CONAN_CMAKE_SYSROOT=$toolchain  # Optional, if we want to define sysroot  
CHOST=$target_host  
AR=$target_host-ar  
AS=$target_host-as  
RANLIB=$target_host-ranlib  
CC=$target_host-$cc_compiler  
CXX=$target_host-$cxx_compiler  
STRIP=$target_host-strip  
RC=$target_host-windres  
  
[settings]  
# We are cross-building to Windows  
os=Windows  
arch=x86  
compiler=gcc  
  
# Adjust to the gcc version of your MinGW package  
compiler.version=10  
compiler.libcxx=libstdc++11  
build_type=Release  
```  
  
```bash  
~/.../boost_user/build >>> i686-w64-mingw32-gcc -v        
                                                                                                                                                       
Using built-in specs.  
COLLECT_GCC=i686-w64-mingw32-gcc  
COLLECT_LTO_WRAPPER=/usr/lib/gcc/i686-w64-mingw32/10.2.0/lto-wrapper  
Target: i686-w64-mingw32  
Configured with: /build/mingw-w64-gcc/src/gcc/configure --prefix=/usr --libexecdir=/usr/lib --target=i686-w64-mingw32 --with-bugurl=https://bugs.archlinux.org/ --enable-languages=c,lto,c++,ada,objc,obj-c++,fortran --enable-shared --enable-static --enable-threads=posix --enable-fully-dynamic-string --enable-libstdcxx-time=yes --enable-libstdcxx-filesystem-ts=yes --with-system-zlib --enable-cloog-backend=isl --enable-lto --enable-libgomp --disable-multilib --enable-checking=release --disable-sjlj-exceptions --with-dwarf2  
Thread model: posix  
Supported LTO compression algorithms: zlib zstd  
gcc version 10.2.0 (GCC)  
```

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:37 UTC  
> Url: https://github.com/boostorg/build/issues/685#issuecomment-850859635  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
