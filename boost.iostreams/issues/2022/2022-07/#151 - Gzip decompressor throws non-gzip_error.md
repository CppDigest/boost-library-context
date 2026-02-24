# #151 - Gzip decompressor throws non-gzip_error [Open]

> Username: chenhao94  
> Created at: 2022-07-26 21:38:14 UTC  
> Updated at: 2022-07-27 19:56:47 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151  

Compiled with Apple Clang 13.1.6 and boost 1.79. When the input is a non-empty invalid string, then the exception thrown by the decompressor cannot be caught as a gzip_error, even though `e.what()` says so.  
```  
~> cat gzip_error.cc && echo == && clang++ -v && echo == && clang++ gzip_error.cc -std=c++20 -L /opt/homebrew/opt/boost/lib/ -lboost_iostreams -o gzip_error && echo == && ./gzip_error  
#include <boost/iostreams/copy.hpp>  
#include <boost/iostreams/device/array.hpp>  
#include <boost/iostreams/filter/gzip.hpp>  
#include <boost/iostreams/filtering_stream.hpp>  
  
#include <iostream>  
  
using namespace std;  
  
void f(std::string input) {  
    std::string decompressed;  
  
    boost::iostreams::filtering_istream in;  
    in.push(boost::iostreams::gzip_decompressor());  
    in.push(boost::iostreams::array_source(input.data(), input.size()));  
  
    try {  
        boost::iostreams::copy(in, boost::iostreams::back_inserter(decompressed));  
    } catch (const boost::iostreams::gzip_error& e) {  
        cout << "gzip_error: " << e.what() << endl;  
    } catch (const std::exception& e) {  
        cout << "other exception: " << e.what() << endl;  
    }  
}  
  
int main() {  
    f("");  
    f("invalid");  
    return 0;  
}  
==  
Apple clang version 13.1.6 (clang-1316.0.21.2.5)  
Target: arm64-apple-darwin21.6.0  
Thread model: posix  
InstalledDir: /Library/Developer/CommandLineTools/usr/bin  
==  
==  
gzip_error: gzip error: unspecified iostream_category error  
other exception: gzip error: unspecified iostream_category error  
```

---

## Comment 1

> Username: mclow  
> Created at: 2022-07-26 23:10:57 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151#issuecomment-1196071191  

If I comment out the second catch block, I get the error:  
"terminating with uncaught exception of type `boost::wrapexcept<boost::iostreams::gzip_error>`: gzip error: unspecified iostream_category error"

---

## Comment 2

> Username: mclow  
> Created at: 2022-07-26 23:40:43 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151#issuecomment-1196100546  

For the empty input case, the exception is being thrown from gzip.hpp, line 475  
    `boost::throw_exception(gzip_error(gzip::bad_header));`  
  
For the non-empty input case, the exception is being thrown from gzip.cpp, line 33  
    `boost::throw_exception(gzip_error(gzip::bad_header));`

---

## Comment 3

> Username: mclow  
> Created at: 2022-07-26 23:51:23 UTC  
> Updated at: 2022-07-26 23:51:37 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151#issuecomment-1196106852  

If you link statically, it works fine:  
`$ clang++ -std=c++2a -I $BOOST boost.cpp /opt/homebrew/opt/boost/lib/*.o -lz -lbz2`

---

## Comment 4

> Username: pdimov  
> Created at: 2022-07-27 07:45:52 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151#issuecomment-1196378300  

If you build Boost yourself instead of using Homebrew, does it still fail? Do the tests for ThrowException pass? Does it work without `-std=c++20`?

---

## Comment 5

> Username: chenhao94  
> Created at: 2022-07-27 17:56:13 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151#issuecomment-1197106900  

> If you build Boost yourself instead of using Homebrew, does it still fail? Do the tests for ThrowException pass? Does it work without -std=c++20?  
  
Changing c++ std won't change the result.  
  
I have not tried to build boost from source, but I tried statically linking to homebrew boost and the issue disappeared as @mclow said above.

---

## Comment 6

> Username: xkszltl  
> Created at: 2022-07-27 19:19:57 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151#issuecomment-1197265516  

Repro on my side as will, with x86 homebrew:  
```  
$ echo == && clang++ -v && echo == && clang++ gzip_error.cc -std=c++20 -L /usr/local/Cellar/boost/1.79.0_1/lib -lboost_iostreams -o gzip_error && echo == && ./gzip_error  
==  
Apple clang version 13.1.6 (clang-1316.0.21.2.5)  
Target: x86_64-apple-darwin21.5.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
==  
==  
gzip_error: gzip error: unspecified iostream_category error  
other exception: gzip error: unspecified iostream_category error  
```

---

## Comment 7

> Username: xkszltl  
> Created at: 2022-07-27 19:26:27 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151#issuecomment-1197272059  

Here's one on CentOS 7 with self-built clang 12 and boost 1.78:  
```  
$ cat gzip_error.cc && echo == && clang++ -v && echo == && clang++ gzip_error.cc -std=c++20 -L/usr/local/lib -isystem/usr/local/include -lboost_iostreams -o gzip_error && echo == && ./gzip_error                                      
#include <boost/iostreams/copy.hpp>  
#include <boost/iostreams/device/array.hpp>  
#include <boost/iostreams/filter/gzip.hpp>  
#include <boost/iostreams/filtering_stream.hpp>  
  
#include <iostream>  
  
using namespace std;  
  
void f(std::string input) {  
    std::string decompressed;  
  
    boost::iostreams::filtering_istream in;  
    in.push(boost::iostreams::gzip_decompressor());  
    in.push(boost::iostreams::array_source(input.data(), input.size()));  
  
    try {  
        boost::iostreams::copy(in, boost::iostreams::back_inserter(decompressed));  
    } catch (const boost::iostreams::gzip_error& e) {  
        cout << "gzip_error: " << e.what() << endl;  
    } catch (const std::exception& e) {  
        cout << "other exception: " << e.what() << endl;  
    }  
}  
  
int main() {  
    f("");  
    f("invalid");  
    return 0;  
}  
  
==  
clang version 12.0.1 (********/tmp.SBf195Jy5t/llvm/clang fed41342a82f5a3a9201819a82bf7a48313e296b)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /usr/local/bin  
Found candidate GCC installation: /opt/rh/devtoolset-6/root/usr/lib/gcc/x86_64-redhat-linux/6.3.1  
Found candidate GCC installation: /opt/rh/devtoolset-7/root/usr/lib/gcc/x86_64-redhat-linux/7  
Found candidate GCC installation: /opt/rh/devtoolset-8/root/usr/lib/gcc/x86_64-redhat-linux/8  
Found candidate GCC installation: /opt/rh/devtoolset-9/root/usr/lib/gcc/x86_64-redhat-linux/9  
Found candidate GCC installation: /usr/lib/gcc/i686-redhat-linux/4.8.2  
Found candidate GCC installation: /usr/lib/gcc/i686-redhat-linux/4.8.5  
Found candidate GCC installation: /usr/lib/gcc/x86_64-redhat-linux/4.8.2  
Found candidate GCC installation: /usr/lib/gcc/x86_64-redhat-linux/4.8.5  
Selected GCC installation: /opt/rh/devtoolset-9/root/usr/lib/gcc/x86_64-redhat-linux/9  
Candidate multilib: .;@m64  
Candidate multilib: 32;@m32  
Selected multilib: .;@m64  
Found CUDA installation: /usr/local/cuda-11.7, version 11.0  
==  
==  
Segmentation fault  
  
$ lldb gzip_error                                                                                                                                                                                                                       
(lldb) target create "gzip_error"  
Current executable set to '********/gzip_error' (x86_64).  
(lldb) r  
Process 135684 launched: '********/gzip_error' (x86_64)  
warning: (x86_64) /lib64/libbz2.so.1 unsupported DW_FORM values: 0x1f20 0x1f21  
Process 135684 stopped  
* thread #1, name = 'gzip_error', stop reason = signal SIGSEGV: invalid address (fault address: 0xfffffffffffffff8)  
    frame #0: 0x00007ffff7fa126c libboost_iostreams.so.1.78.0`boost::iostreams::detail::gzip_header::reset() + 12  
libboost_iostreams.so.1.78.0`boost::iostreams::detail::gzip_header::reset:  
->  0x7ffff7fa126c <+12>: movl   -0x8(%rax), %eax  
    0x7ffff7fa126f <+15>: testl  %eax, %eax  
    0x7ffff7fa1271 <+17>: movq   (%rdi), %rax  
    0x7ffff7fa1274 <+20>: jle    0x7ffff7fa12e0            ; <+128>  
(lldb) bt  
* thread #1, name = 'gzip_error', stop reason = signal SIGSEGV: invalid address (fault address: 0xfffffffffffffff8)  
  * frame #0: 0x00007ffff7fa126c libboost_iostreams.so.1.78.0`boost::iostreams::detail::gzip_header::reset() + 12  
    frame #1: 0x00000000002173c3 gzip_error`boost::iostreams::detail::gzip_header::gzip_header() + 51  
    frame #2: 0x0000000000210e6a gzip_error`boost::iostreams::basic_gzip_decompressor<std::__1::allocator<char> >::basic_gzip_decompressor(int, long) + 106  
    frame #3: 0x0000000000210900 gzip_error`f(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >) + 80  
    frame #4: 0x0000000000210c4a gzip_error`main + 42  
    frame #5: 0x00007ffff7517555 libc.so.6`__libc_start_main(main=(gzip_error`main), argc=1, argv=0x00007fffffffdee8, init=<unavailable>, fini=<unavailable>, rtld_fini=<unavailable>, stack_end=0x00007fffffffded8) at libc-start.c:266  
    frame #6: 0x00000000002107f9 gzip_error`_start + 41  
```

---

## Comment 8

> Username: xkszltl  
> Created at: 2022-07-27 19:36:13 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151#issuecomment-1197281076  

Similar crash on Debian 11 with self built clang-12 and boost 1.77  
```  
$ cat gzip_error.cc && echo == && clang++ -v && echo == && clang++ gzip_error.cc -std=c++20 -L/usr/local/lib -isystem /usr/local/include -lboost_iostreams -o gzip_error && echo == && ./gzip_error  
#include <boost/iostreams/copy.hpp>  
#include <boost/iostreams/device/array.hpp>  
#include <boost/iostreams/filter/gzip.hpp>  
#include <boost/iostreams/filtering_stream.hpp>  
  
#include <iostream>  
  
using namespace std;  
  
void f(std::string input) {  
    std::string decompressed;  
  
    boost::iostreams::filtering_istream in;  
    in.push(boost::iostreams::gzip_decompressor());  
    in.push(boost::iostreams::array_source(input.data(), input.size()));  
  
    try {  
        boost::iostreams::copy(in, boost::iostreams::back_inserter(decompressed));  
    } catch (const boost::iostreams::gzip_error& e) {  
        cout << "gzip_error: " << e.what() << endl;  
    } catch (const std::exception& e) {  
        cout << "other exception: " << e.what() << endl;  
    }  
}  
  
int main() {  
    f("");  
    f("invalid");  
    return 0;  
}  
==  
clang version 12.0.1 (https://git.codingcafe.org/Mirrors/llvm/llvm-project.git fed41342a82f5a3a9201819a82bf7a48313e296b)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /usr/local/bin  
Found candidate GCC installation: /usr/lib/gcc/x86_64-linux-gnu/10  
Found candidate GCC installation: /usr/lib/gcc/x86_64-linux-gnu/9  
Selected GCC installation: /usr/lib/gcc/x86_64-linux-gnu/10  
Candidate multilib: .;@m64  
Candidate multilib: 32;@m32  
Candidate multilib: x32;@mx32  
Selected multilib: .;@m64  
==  
==  
Segmentation fault  
  
$ lldb gzip_error  
(lldb) target create "gzip_error"  
Current executable set to '********/gzip_error' (x86_64).  
(lldb) r  
Process 3846619 launched: '********/gzip_error' (x86_64)  
Process 3846619 stopped  
* thread #1, name = 'gzip_error', stop reason = signal SIGSEGV: invalid address (fault address: 0x0)  
    frame #0: 0x00007ffff7f89073 libboost_iostreams.so.1.77.0`boost::iostreams::detail::gzip_header::reset() + 19  
libboost_iostreams.so.1.77.0`boost::iostreams::detail::gzip_header::reset:  
->  0x7ffff7f89073 <+19>: movb   $0x0, (%rax)  
    0x7ffff7f89076 <+22>: movq   0x20(%rdi), %rax  
    0x7ffff7f8907a <+26>: movq   $0x0, 0x28(%rdi)  
    0x7ffff7f89082 <+34>: movb   $0x0, (%rax)  
(lldb) bt  
* thread #1, name = 'gzip_error', stop reason = signal SIGSEGV: invalid address (fault address: 0x0)  
  * frame #0: 0x00007ffff7f89073 libboost_iostreams.so.1.77.0`boost::iostreams::detail::gzip_header::reset() + 19  
    frame #1: 0x00000000002176e3 gzip_error`boost::iostreams::detail::gzip_header::gzip_header() + 51  
    frame #2: 0x000000000021118a gzip_error`boost::iostreams::basic_gzip_decompressor<std::__1::allocator<char> >::basic_gzip_decompressor(int, long) + 106  
    frame #3: 0x0000000000210c20 gzip_error`f(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >) + 80  
    frame #4: 0x0000000000210f6a gzip_error`main + 42  
    frame #5: 0x00007ffff7b59d0a libc.so.6`__libc_start_main(main=(gzip_error`main), argc=1, argv=0x00007fffffffe498, init=<unavailable>, fini=<unavailable>, rtld_fini=<unavailable>, stack_end=0x00007fffffffe488) at libc-start.c:308:16  
    frame #6: 0x0000000000210b0a gzip_error`_start + 42  
```  
  
or with distro stock boost 1.74  
```  
(lldb) target create "gzip_error"  
Current executable set to '/home/xkszltl/Projects/roaster-public/gzip_error' (x86_64).  
(lldb) r  
Process 3851326 launched: '/home/xkszltl/Projects/roaster-public/gzip_error' (x86_64)  
Process 3851326 stopped  
* thread #1, name = 'gzip_error', stop reason = signal SIGSEGV: invalid address (fault address: 0x0)  
    frame #0: 0x00007ffff7f894f3 libboost_iostreams.so.1.74.0`boost::iostreams::detail::gzip_header::reset() + 19  
libboost_iostreams.so.1.74.0`boost::iostreams::detail::gzip_header::reset:  
->  0x7ffff7f894f3 <+19>: movb   $0x0, (%rax)  
    0x7ffff7f894f6 <+22>: movq   0x20(%rdi), %rax  
    0x7ffff7f894fa <+26>: movq   $0x0, 0x28(%rdi)  
    0x7ffff7f89502 <+34>: movb   $0x0, (%rax)  
(lldb) bt  
* thread #1, name = 'gzip_error', stop reason = signal SIGSEGV: invalid address (fault address: 0x0)  
  * frame #0: 0x00007ffff7f894f3 libboost_iostreams.so.1.74.0`boost::iostreams::detail::gzip_header::reset() + 19  
    frame #1: 0x00000000002176b3 gzip_error`boost::iostreams::detail::gzip_header::gzip_header() + 51  
    frame #2: 0x000000000021115a gzip_error`boost::iostreams::basic_gzip_decompressor<std::__1::allocator<char> >::basic_gzip_decompressor(int, long) + 106  
    frame #3: 0x0000000000210bf0 gzip_error`f(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char> >) + 80  
    frame #4: 0x0000000000210f3a gzip_error`main + 42  
    frame #5: 0x00007ffff7b15d0a libc.so.6`__libc_start_main(main=(gzip_error`main), argc=1, argv=0x00007fffffffe498, init=<unavailable>, fini=<unavailable>, rtld_fini=<unavailable>, stack_end=0x00007fffffffe488) at libc-start.c:308:16  
    frame #6: 0x0000000000210ada gzip_error`_start + 42  
```

---

## Comment 9

> Username: xkszltl  
> Created at: 2022-07-27 19:44:56 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151#issuecomment-1197289848  

Added `-g` for CentOS case:  
```  
(lldb) target create "gzip_error"  
Current executable set to '********/gzip_error' (x86_64).  
(lldb) r  
Process 171637 launched: '********/gzip_error' (x86_64)  
warning: (x86_64) /lib64/libbz2.so.1 unsupported DW_FORM values: 0x1f20 0x1f21  
Process 171637 stopped  
* thread #1, name = 'gzip_error', stop reason = signal SIGSEGV: invalid address (fault address: 0xfffffffffffffff8)  
    frame #0: 0x00007ffff7fa126c libboost_iostreams.so.1.78.0`boost::iostreams::detail::gzip_header::reset() + 12  
libboost_iostreams.so.1.78.0`boost::iostreams::detail::gzip_header::reset:  
->  0x7ffff7fa126c <+12>: movl   -0x8(%rax), %eax  
    0x7ffff7fa126f <+15>: testl  %eax, %eax  
    0x7ffff7fa1271 <+17>: movq   (%rdi), %rax  
    0x7ffff7fa1274 <+20>: jle    0x7ffff7fa12e0            ; <+128>  
(lldb) bt  
* thread #1, name = 'gzip_error', stop reason = signal SIGSEGV: invalid address (fault address: 0xfffffffffffffff8)  
  * frame #0: 0x00007ffff7fa126c libboost_iostreams.so.1.78.0`boost::iostreams::detail::gzip_header::reset() + 12  
    frame #1: 0x00000000002173d3 gzip_error`boost::iostreams::detail::gzip_header::gzip_header(this=0x00007fffffffdc20) at gzip.hpp:327:21  
    frame #2: 0x0000000000210e7a gzip_error`boost::iostreams::basic_gzip_decompressor<std::__1::allocator<char> >::basic_gzip_decompressor(this=0x00007fffffffdc10, window_bits=15, buffer_size=4096) at gzip.hpp:413:5  
    frame #3: 0x0000000000210910 gzip_error`f(input="") at gzip_error.cc:14:13  
    frame #4: 0x0000000000210c5a gzip_error`main at gzip_error.cc:27:5  
    frame #5: 0x00007ffff7517555 libc.so.6`__libc_start_main(main=(gzip_error`main at gzip_error.cc:26), argc=1, argv=0x00007fffffffdee8, init=<unavailable>, fini=<unavailable>, rtld_fini=<unavailable>, stack_end=0x00007fffffffded8) at libc-start.c:266  
    frame #6: 0x0000000000210809 gzip_error`_start + 41  
```

---

## Comment 10

> Username: xkszltl  
> Created at: 2022-07-27 19:50:19 UTC  
> Updated at: 2022-07-27 19:50:38 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151#issuecomment-1197294976  

If I use libstdc++ on Linux it'll works, Both the apple clang and my self-built llvm on linux are pointing to libc++ by default.  
```  
$ cat gzip_error.cc && echo == && clang++ -v && echo == && clang++ gzip_error.cc -std=c++20 -L/usr/local/lib -isystem/usr/local/include -lboost_iostreams -o gzip_error -g3 -stdlib=libstdc++ && echo == && ./gzip_error  
#include <boost/iostreams/copy.hpp>  
#include <boost/iostreams/device/array.hpp>  
#include <boost/iostreams/filter/gzip.hpp>  
#include <boost/iostreams/filtering_stream.hpp>  
  
#include <iostream>  
  
using namespace std;  
  
void f(std::string input) {  
    std::string decompressed;  
  
    boost::iostreams::filtering_istream in;  
    in.push(boost::iostreams::gzip_decompressor());  
    in.push(boost::iostreams::array_source(input.data(), input.size()));  
  
    try {  
        boost::iostreams::copy(in, boost::iostreams::back_inserter(decompressed));  
    } catch (const boost::iostreams::gzip_error& e) {  
        cout << "gzip_error: " << e.what() << endl;  
    } catch (const std::exception& e) {  
        cout << "other exception: " << e.what() << endl;  
    }  
}  
  
int main() {  
    f("");  
    f("invalid");  
    return 0;  
}  
==  
clang version 12.0.1 (/media/Scratch/tmp.SBf195Jy5t/llvm/clang fed41342a82f5a3a9201819a82bf7a48313e296b)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /usr/local/bin  
Found candidate GCC installation: /opt/rh/devtoolset-6/root/usr/lib/gcc/x86_64-redhat-linux/6.3.1  
Found candidate GCC installation: /opt/rh/devtoolset-7/root/usr/lib/gcc/x86_64-redhat-linux/7  
Found candidate GCC installation: /opt/rh/devtoolset-8/root/usr/lib/gcc/x86_64-redhat-linux/8  
Found candidate GCC installation: /opt/rh/devtoolset-9/root/usr/lib/gcc/x86_64-redhat-linux/9  
Found candidate GCC installation: /usr/lib/gcc/i686-redhat-linux/4.8.2  
Found candidate GCC installation: /usr/lib/gcc/i686-redhat-linux/4.8.5  
Found candidate GCC installation: /usr/lib/gcc/x86_64-redhat-linux/4.8.2  
Found candidate GCC installation: /usr/lib/gcc/x86_64-redhat-linux/4.8.5  
Selected GCC installation: /opt/rh/devtoolset-9/root/usr/lib/gcc/x86_64-redhat-linux/9  
Candidate multilib: .;@m64  
Candidate multilib: 32;@m32  
Selected multilib: .;@m64  
Found CUDA installation: /usr/local/cuda-11.7, version 11.0  
==  
==  
gzip_error: gzip error  
gzip_error: gzip error  
```

---

## Comment 11

> Username: xkszltl  
> Created at: 2022-07-27 19:52:08 UTC  
> Updated at: 2022-07-27 19:52:31 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151#issuecomment-1197296859  

And gcc-11 (devtoolset-11 on CentOS 7) is fine as well, it's using libstdc++.

---

## Comment 12

> Username: xkszltl  
> Created at: 2022-07-27 19:56:47 UTC  
> Url: https://github.com/boostorg/iostreams/issues/151#issuecomment-1197301322  

BTW my boost on Linux was initially built by gcc with libstdc++, so not sure if the mixed ABI contribute to the segfault. But I assume the homebrew one on mac is built by the same compiler (apple clang)?
