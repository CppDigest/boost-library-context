# #116 - Link error in header-only mode (undefined reference to `to_string`) [Closed]

> Username: ldeng-ustc  
> Created at: 2021-12-01 02:39:38 UTC  
> Updated at: 2022-09-02 07:39:12 UTC  
> Closed at: 2022-09-02 07:39:12 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/116  

Just build the simplest example:  
  
```cpp  
#include <iostream>  
#include "boost/stacktrace/stacktrace.hpp"  
  
using namespace std;  
int main() {  
	cout << boost::stacktrace::stacktrace() << endl;  
	return 0;  
}  
```  
  
And get the link error:  
  
```bash  
$ g++  test_stacktrace.cpp  
/usr/bin/ld: /tmp/ccrbJANZ.o: in function `std::__cxx11::basic_string<char, std::char_traits<char>, std::allocator<char> > boost::stacktrace::to_string<std::allocator<boost::stacktrace::frame> >(boost::stacktrace::basic_stacktrace<std::allocator<boost::stacktrace::frame> > const&)':  
test_stacktrace.cpp:(.text._ZN5boost10stacktrace9to_stringISaINS0_5frameEEEENSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERKNS0_16basic_stacktraceIT_EE[_ZN5boost10stacktrace9to_stringISaINS0_5frameEEEENSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERKNS0_16basic_stacktraceIT_EE]+0x7b): undefined reference to `boost::stacktrace::detail::to_string[abi:cxx11](boost::stacktrace::frame const*, unsigned long)'  
collect2: error: ld returned 1 exit status  
```  
  
Add `-ldl` flag does not change any thing.  
  
I have to build `stacktrace` library and add `-DBOOST_STACKTRACE_LINK -lboost_stacktrace_backtrace -ldl -lbacktrace`, but in document, `stacktrack` is header-only by defaults, so what's wrong?  
  
I run these on Ubuntu 20.04 in WSL2, and gcc version 9.3.0, and boost version is 1.77.0. And I find similar issue in [stackoverflow(ru)](https://ru.stackoverflow.com/questions/925598/%D0%9E%D1%88%D0%B8%D0%B1%D0%BA%D0%B0-%D0%BF%D1%80%D0%B8-%D0%BF%D0%BE%D0%BF%D1%8B%D1%82%D0%BA%D0%B5-%D1%81%D0%BA%D0%BE%D0%BC%D0%BF%D0%B8%D0%BB%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D1%82%D1%8C-%D1%87%D1%82%D0%BE-%D1%82%D0%BE-%D1%81-booststacktrace)  
  
```bash  
$ wslfetch  
Windows 10 Linux Subsystem                         
BUILD:    19043                                    
BRANCH:   vb_release                               
RELEASE:  Ubuntu 20.04.2 LTS                       
KERNEL:   Linux 5.10.16.3-microsoft-standard-WSL2  
  
$ gcc -v  
Using built-in specs.  
COLLECT_GCC=gcc  
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-linux-gnu/9/lto-wrapper  
OFFLOAD_TARGET_NAMES=nvptx-none:hsa  
OFFLOAD_TARGET_DEFAULT=1  
Target: x86_64-linux-gnu  
Configured with: ../src/configure -v --with-pkgversion='Ubuntu 9.3.0-17ubuntu1~20.04' --with-bugurl=file:///usr/share/doc/gcc-9/README.Bugs --enable-languages=c,ada,c++,go,brig,d,fortran,objc,obj-c++,gm2 --prefix=/usr --with-gcc-major-version-only --program-suffix=-9 --program-prefix=x86_64-linux-gnu- --enable-shared --enable-linker-build-id --libexecdir=/usr/lib --without-included-gettext --enable-threads=posix --libdir=/usr/lib --enable-nls --enable-clocale=gnu --enable-libstdcxx-debug --enable-libstdcxx-time=yes --with-default-libstdcxx-abi=new --enable-gnu-unique-object --disable-vtable-verify --enable-plugin --enable-default-pie --with-system-zlib --with-target-system-zlib=auto --enable-objc-gc=auto --enable-multiarch --disable-werror --with-arch-32=i686 --with-abi=m64 --with-multilib-list=m32,m64,mx32 --enable-multilib --with-tune=generic --enable-offload-targets=nvptx-none=/build/gcc-9-HskZEa/gcc-9-9.3.0/debian/tmp-nvptx/usr,hsa --without-cuda-driver --enable-checking=release --build=x86_64-linux-gnu --host=x86_64-linux-gnu --target=x86_64-linux-gnu  
Thread model: posix  
gcc version 9.3.0 (Ubuntu 9.3.0-17ubuntu1~20.04)  
```

---

## Comment 1

> Username: bgemmill  
> Created at: 2021-12-14 17:34:57 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/116#issuecomment-993817078  

I've been struggling with this too today. It looks like something changed with the b2 build process between boost 1.77 and 1.78. In trying to solve this problem, I tried to link to `libboost_stacktrace_backtrace` and found that it wasn't being copied out of b2 build's staging area either.  
  
Since boost::stacktrace is supposed to be header only, a hack for now is to include the `.ipp` file for your unwinding implementation in *one* source file in your project, and then also globally defining which implementation to use.  
  
In my case with libbacktrace:  
```  
 // put this in one source file  
#include <boost/stacktrace/detail/frame_unwind.ipp>  
// define this globally  
BOOST_STACKTRACE_USE_BACKTRACE=1  
```  
and also link with libbacktrace and libdl

---

## Comment 2

> Username: apolukhin  
> Created at: 2022-09-01 18:27:28 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/116#issuecomment-1234635657  

As a workaround include `#include "boost/stacktrace.hpp"` instead of `#include "boost/stacktrace/stacktrace.hpp"`  
  
https://godbolt.org/z/KPTxaM1sn  
  
Will fix the issue in a few days
