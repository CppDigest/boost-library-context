# #38 - Various test failures uncovered during local testing [Closed]

> Username: pdimov  
> Created at: 2017-07-09 21:31:07 UTC  
> Updated at: 2019-01-05 18:30:44 UTC  
> Closed at: 2019-01-05 18:30:44 UTC  
> Url: https://github.com/boostorg/type_traits/issues/38  

This is what I have (CI_SUPPRESS_KNOWN_ISSUES has been defined):  
  
Cygwin g++ 6.3.0:  
  
```  
testing.capture-output ..\..\bin.v2\libs\type_traits\test\alignment_of_a2_test.test\gcc-cxx03-6.3.0\debug\alignment_of_a2_test.run  
====== BEGIN OUTPUT ======  
test\alignment_of_a2_test.cpp:95: The expression: "::boost::alignment_of<double>::value" had an invalid value (found 8, expected 4)  
test\alignment_of_a2_test.cpp:98: The expression: "::boost::alignment_of< ::boost::long_long_type>::value" had an invalid value (found 8, expected 4)  
  
EXIT STATUS: 2  
====== END OUTPUT ======  
```  
  
```  
testing.capture-output ..\..\bin.v2\libs\type_traits\test\alignment_of_test.test\gcc-cxx03-6.3.0\debug\alignment_of_test.run  
====== BEGIN OUTPUT ======  
test\alignment_of_test.cpp:90: The expression: "::boost::alignment_of<double>::value" had an invalid value (found 8, expected 4)  
test\alignment_of_test.cpp:93: The expression: "::boost::alignment_of< ::boost::long_long_type>::value" had an invalid value (found 8, expected 4)  
  
EXIT STATUS: 2  
====== END OUTPUT ======  
```  
  
(same for c++11, 14, 1z)  
  
Cygwin clang++ 4.0.1 -std=c++1z:  
  
```  
clang-linux.compile.c++.without-pth ..\..\bin.v2\libs\type_traits\test\has_nothrow_destructor_test.test\clang-linux-cxx1z-4.0.1\debug\has_nothrow_destructor_test.obj  
In file included from test\has_nothrow_destructor_test.cpp:12:  
test/test.hpp:210:9: warning: 'const' qualifier on reference type 'r_type' (aka'int &') has no effect [-Wignored-qualifiers]  
typedef const r_type cr_type;  
        ^~~~~~  
test\has_nothrow_destructor_test.cpp:37:21: error: ISO C++1z does not allow dynamic exception specifications [-Wdynamic-exception-spec]  
   ~throwing_base() throw(int);  
                    ^~~~~~~~~~  
test\has_nothrow_destructor_test.cpp:37:21: note: use 'noexcept(false)' instead  
   ~throwing_base() throw(int);  
                    ^~~~~~~~~~  
                    noexcept(false)  
1 warning and 1 error generated.  
```  
  
Appveyor has Cygwin:  
  
https://github.com/boostorg/system/blob/develop/appveyor.yml#L25  
  
Travis has clang++ 4:  
  
https://github.com/boostorg/smart_ptr/blob/develop/.travis.yml#L479

---

## Comment 1

> Username: pdimov  
> Created at: 2017-07-09 21:48:11 UTC  
> Url: https://github.com/boostorg/type_traits/issues/38#issuecomment-313967698  

msvc-11.0 failure edited out, was a local issue.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2017-07-12 19:05:40 UTC  
> Url: https://github.com/boostorg/type_traits/issues/38#issuecomment-314866720  

I'm unable to reproduce with either cygwin-23 or cygwin-64, both are   
using the latest gcc-6.3.0-2:  
  
$ g++ -v  
Using built-in specs.  
COLLECT_GCC=g++  
COLLECT_LTO_WRAPPER=/usr/lib/gcc/x86_64-pc-cygwin/6.3.0/lto-wrapper.exe  
Target: x86_64-pc-cygwin  
Configured with:   
/cygdrive/i/szsz/tmpp/gcc/gcc-6.3.0-2.x86_64/src/gcc-6.3.0/configure   
--srcdir=/cygdrive/i/szsz/tmpp/gcc/gcc-6.3.0-2.x86_64/src/gcc-6.3.0   
--prefix=/usr --exec-prefix=/usr --localstatedir=/var --sysconfdir=/etc   
--docdir=/usr/share/doc/gcc --htmldir=/usr/share/doc/gcc/html -C   
--build=x86_64-pc-cygwin --host=x86_64-pc-cygwin   
--target=x86_64-pc-cygwin --without-libiconv-prefix   
--without-libintl-prefix --libexecdir=/usr/lib --enable-shared   
--enable-shared-libgcc --enable-static   
--enable-version-specific-runtime-libs --enable-bootstrap   
--enable-__cxa_atexit --with-dwarf2 --with-tune=generic   
--enable-languages=ada,c,c++,fortran,lto,objc,obj-c++ --enable-graphite   
--enable-threads=posix --enable-libatomic --enable-libcilkrts   
--enable-libgomp --enable-libitm --enable-libquadmath   
--enable-libquadmath-support --enable-libssp --enable-libada   
--disable-symvers --with-gnu-ld --with-gnu-as   
--with-cloog-include=/usr/include/cloog-isl --without-libiconv-prefix   
--without-libintl-prefix --with-system-zlib --enable-linker-build-id   
--with-default-libstdcxx-abi=gcc4-compatible  
Thread model: posix  
gcc version 6.3.0 (GCC)  
  
Can you check your exact gcc version and report back?  
  
  
Thanks, John.  
  
  
  
On 09/07/2017 22:31, Peter Dimov wrote:  
>  
> This is what I have (CI_SUPPRESS_KNOWN_ISSUES has been defined):  
>  
> Cygwin g++ 6.3.0:  
>  
> |testing.capture-output   
> ..\..\bin.v2\libs\type_traits\test\alignment_of_a2_test.test\gcc-cxx03-6.3.0\debug\alignment_of_a2_test.run   
> ====== BEGIN OUTPUT ====== test\alignment_of_a2_test.cpp:95: The   
> expression: "::boost::alignment_of<double>::value" had an invalid   
> value (found 8, expected 4) test\alignment_of_a2_test.cpp:98: The   
> expression: "::boost::alignment_of< ::boost::long_long_type>::value"   
> had an invalid value (found 8, expected 4) EXIT STATUS: 2 ====== END   
> OUTPUT ====== |  
> |testing.capture-output   
> ..\..\bin.v2\libs\type_traits\test\alignment_of_test.test\gcc-cxx03-6.3.0\debug\alignment_of_test.run   
> ====== BEGIN OUTPUT ====== test\alignment_of_test.cpp:90: The   
> expression: "::boost::alignment_of<double>::value" had an invalid   
> value (found 8, expected 4) test\alignment_of_test.cpp:93: The   
> expression: "::boost::alignment_of< ::boost::long_long_type>::value"   
> had an invalid value (found 8, expected 4) EXIT STATUS: 2 ====== END   
> OUTPUT ====== |  
>  
> (same for c++11, 14, 1z)  
>  
> msvc-11.0:  
>  
> |testing.capture-output   
> ..\..\bin.v2\libs\type_traits\test\tricky_rvalue_test.test\msvc-11.0\debug\threading-multi\tricky_rvalue_test.run   
> ====== BEGIN OUTPUT ====== test\tricky_rvalue_test.cpp:28: The   
> expression: "(::boost::is_convertible<int&&, int&>::value)" had an   
> invalid value (found 1, expected 0) test\tricky_rvalue_test.cpp:29:   
> The expression: "(::boost::is_convertible<int(), int(&&)()>::value)"   
> had an invalid value (found 0, expected 1) EXIT STATUS: 2 ====== END   
> OUTPUT ====== |  
>  
> Cygwin clang++ 4.0.1 -std=c++1z:  
>  
> |clang-linux.compile.c++.without-pth   
> ..\..\bin.v2\libs\type_traits\test\has_nothrow_destructor_test.test\clang-linux-cxx1z-4.0.1\debug\has_nothrow_destructor_test.obj   
> In file included from test\has_nothrow_destructor_test.cpp:12:   
> test/test.hpp:210:9: warning: 'const' qualifier on reference type   
> 'r_type' (aka'int &') has no effect [-Wignored-qualifiers] typedef   
> const r_type cr_type; ^~~~~~   
> test\has_nothrow_destructor_test.cpp:37:21: error: ISO C++1z does not   
> allow dynamic exception specifications [-Wdynamic-exception-spec]   
> ~throwing_base() throw(int); ^~~~~~~~~~   
> test\has_nothrow_destructor_test.cpp:37:21: note: use   
> 'noexcept(false)' instead ~throwing_base() throw(int); ^~~~~~~~~~   
> noexcept(false) 1 warning and 1 error generated. |  
>  
> Appveyor has Cygwin:  
>  
> https://github.com/boostorg/system/blob/develop/appveyor.yml#L25  
>  
> Travis has clang++ 4:  
>  
> https://github.com/boostorg/smart_ptr/blob/develop/.travis.yml#L479  
>  
> Not sure about the msvc-11.0 one, as you have it on Appveyor. I could   
> be missing an update.  
>  
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly, view it on GitHub   
> <https://github.com/boostorg/type_traits/issues/38>, or mute the   
> thread   
> <https://github.com/notifications/unsubscribe-auth/AEx5OCI4itfIBe-jRh85YdHzripsEglCks5sMUabgaJpZM4OSMWj>.  
>  
  
  
---  
This email has been checked for viruses by AVG.  
http://www.avg.com

---

## Comment 3

> Username: pdimov  
> Created at: 2017-07-12 19:35:54 UTC  
> Url: https://github.com/boostorg/type_traits/issues/38#issuecomment-314873902  

Odd.  
  
```  
C:\Projects\boost-git\boost\libs\type_traits>g++ -v  
Using built-in specs.  
COLLECT_GCC=g++  
COLLECT_LTO_WRAPPER=/usr/lib/gcc/i686-pc-cygwin/6.3.0/lto-wrapper.exe  
Target: i686-pc-cygwin  
Configured with: /cygdrive/i/szsz/tmpp/gcc/gcc-6.3.0-2.i686/src/gcc-6.3.0/config  
ure --srcdir=/cygdrive/i/szsz/tmpp/gcc/gcc-6.3.0-2.i686/src/gcc-6.3.0 --prefix=/  
usr --exec-prefix=/usr --localstatedir=/var --sysconfdir=/etc --docdir=/usr/shar  
e/doc/gcc --htmldir=/usr/share/doc/gcc/html -C --build=i686-pc-cygwin --host=i68  
6-pc-cygwin --target=i686-pc-cygwin --without-libiconv-prefix --without-libintl-  
prefix --libexecdir=/usr/lib --enable-shared --enable-shared-libgcc --enable-sta  
tic --enable-version-specific-runtime-libs --enable-bootstrap --enable-__cxa_ate  
xit --with-dwarf2 --with-arch=i686 --with-tune=generic --disable-sjlj-exceptions  
 --enable-languages=ada,c,c++,fortran,lto,objc,obj-c++ --enable-graphite --enabl  
e-threads=posix --enable-libatomic --enable-libcilkrts --enable-libgomp --enable  
-libitm --enable-libquadmath --enable-libquadmath-support --enable-libssp --enab  
le-libada --disable-symvers --with-gnu-ld --with-gnu-as --with-cloog-include=/us  
r/include/cloog-isl --without-libiconv-prefix --without-libintl-prefix --with-sy  
stem-zlib --enable-linker-build-id --with-default-libstdcxx-abi=gcc4-compatible  
Thread model: posix  
gcc version 6.3.0 (GCC)  
```  
  
```  
C:\Projects\boost-git\boost\libs\type_traits>b2 test//alignment_of_test toolset=  
gcc-cxx03  
Performing configuration checks  
  
    - 32-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
...patience...  
...found 803 targets...  
...updating 2 targets...  
testing.capture-output ..\..\bin.v2\libs\type_traits\test\alignment_of_test.test  
\gcc-cxx03-6.3.0\debug\alignment_of_test.run  
====== BEGIN OUTPUT ======  
test\alignment_of_test.cpp:90: The expression: "::boost::alignment_of<double>::v  
alue" had an invalid value (found 8, expected 4)  
test\alignment_of_test.cpp:93: The expression: "::boost::alignment_of< ::boost::  
long_long_type>::value" had an invalid value (found 8, expected 4)  
  
EXIT STATUS: 2  
====== END OUTPUT ======  
```

---

## Comment 4

> Username: pdimov  
> Created at: 2017-07-15 11:42:20 UTC  
> Url: https://github.com/boostorg/type_traits/issues/38#issuecomment-315528676  

Mystery solved, I use `-std=c++03` and you use `-std=gnu++03`. The latter works, the former fails as above.

---

## Comment 5

> Username: jzmaddock  
> Created at: 2017-10-25 18:51:28 UTC  
> Url: https://github.com/boostorg/type_traits/issues/38#issuecomment-339433224  

Nope, just looked at this again with both 64 and 32 bit versions of mingw 6.3 and 7.2 and still can't reproduce :(  
  
I do dimly remember that GCC's `__alignof__` sometimes lies. So I think the thing for you to do is file a PR against intrinsics.hpp so that we don't use the intrinsic for the cases which don't work?
