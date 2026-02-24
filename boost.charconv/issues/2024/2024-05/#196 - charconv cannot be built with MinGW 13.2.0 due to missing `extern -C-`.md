# #196 - charconv cannot be built with MinGW 13.2.0 due to missing `extern "C"` [Closed]

> Username: TsXor  
> Created at: 2024-05-09 10:50:27 UTC  
> Updated at: 2024-05-13 07:07:33 UTC  
> Closed at: 2024-05-13 07:07:33 UTC  
> Url: https://github.com/boostorg/charconv/issues/196  

boost: 1.85.0 release 7z ball  
  
build commands:  
```  
./bootstrap.bat mingw  
./b2 toolset=gcc  
```  
  
gcc version: (installed with `choco install mingw`)  
```  
PS > gcc -v  
Using built-in specs.  
COLLECT_GCC=C:\ProgramData\mingw64\mingw64\bin\gcc.exe  
COLLECT_LTO_WRAPPER=C:/ProgramData/mingw64/mingw64/bin/../libexec/gcc/x86_64-w64-mingw32/13.2.0/lto-wrapper.exe  
Target: x86_64-w64-mingw32  
Configured with: ../../../src/gcc-13.2.0/configure --host=x86_64-w64-mingw32 --build=x86_64-w64-mingw32 --target=x86_64-w64-mingw32 --prefix=/mingw64 --with-sysroot=/c/buildroot/x86_64-1320-posix-seh-ucrt-rt_v11-rev0/mingw64 --enable-host-shared --disable-multilib --enable-languages=c,c++,fortran,lto --enable-libstdcxx-time=yes --enable-threads=posix --enable-libgomp --enable-libatomic --enable-lto --enable-graphite --enable-checking=release --enable-fully-dynamic-string --enable-version-specific-runtime-libs --enable-libstdcxx-filesystem-ts=yes --disable-libssp --disable-libstdcxx-pch --disable-libstdcxx-debug --enable-bootstrap --disable-rpath --disable-win32-registry --disable-nls --disable-werror --disable-symvers --with-gnu-as --with-gnu-ld --with-arch=nocona --with-tune=core2 --with-libiconv --with-system-zlib --with-gmp=/c/buildroot/prerequisites/x86_64-w64-mingw32-static --with-mpfr=/c/buildroot/prerequisites/x86_64-w64-mingw32-static --with-mpc=/c/buildroot/prerequisites/x86_64-w64-mingw32-static --with-isl=/c/buildroot/prerequisites/x86_64-w64-mingw32-static --with-pkgversion='x86_64-posix-seh-rev0, Built by MinGW-Builds project' --with-bugurl=https://github.com/niXman/mingw-builds CFLAGS='-O2 -pipe -fno-ident -I/c/buildroot/x86_64-1320-posix-seh-ucrt-rt_v11-rev0/mingw64/opt/include -I/c/buildroot/prerequisites/x86_64-zlib-static/include -I/c/buildroot/prerequisites/x86_64-w64-mingw32-static/include' CXXFLAGS='-O2 -pipe -fno-ident -I/c/buildroot/x86_64-1320-posix-seh-ucrt-rt_v11-rev0/mingw64/opt/include -I/c/buildroot/prerequisites/x86_64-zlib-static/include -I/c/buildroot/prerequisites/x86_64-w64-mingw32-static/include' CPPFLAGS=' -I/c/buildroot/x86_64-1320-posix-seh-ucrt-rt_v11-rev0/mingw64/opt/include -I/c/buildroot/prerequisites/x86_64-zlib-static/include -I/c/buildroot/prerequisites/x86_64-w64-mingw32-static/include' LDFLAGS='-pipe -fno-ident -L/c/buildroot/x86_64-1320-posix-seh-ucrt-rt_v11-rev0/mingw64/opt/lib -L/c/buildroot/prerequisites/x86_64-zlib-static/lib -L/c/buildroot/prerequisites/x86_64-w64-mingw32-static/lib ' LD_FOR_TARGET=/c/buildroot/x86_64-1320-posix-seh-ucrt-rt_v11-rev0/mingw64/bin/ld.exe --with-boot-ldflags=' -Wl,--disable-dynamicbase -static-libstdc++ -static-libgcc'  
Thread model: posix  
Supported LTO compression algorithms: zlib  
gcc version 13.2.0 (x86_64-posix-seh-rev0, Built by MinGW-Builds project)  
```  
  
build output: [build_fail.log](https://github.com/boostorg/charconv/files/15261264/build_fail.log)  
  
By reading the error info, I found that it is caused by language linkage, and there should be somewhere missing `extern "C"` wrapping.  
It turned out that `x86intrin.h` have no `extern "C"` wrapping, but `./boost/charconv/detail/config.hpp:82` doesn't know this.  
https://github.com/boostorg/charconv/blob/ecdca0865d4d8fcfbf8e5b48914f2b5711c418ad/include/boost/charconv/detail/config.hpp#L82  
After wrapping it like the following, I could successfully compile charconv.  
```C++  
extern "C" {  
#  include <x86intrin.h>  
}  
```  
build output with patch above:  [build_patched.log](https://github.com/boostorg/charconv/files/15261267/build_patched.log)
