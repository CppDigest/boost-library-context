# #298 - `fs::unique_path()` segfaults under mingw with `-fno-rtti` [Closed]

> Username: cmazakas  
> Created at: 2023-12-19 22:48:38 UTC  
> Updated at: 2023-12-20 23:54:25 UTC  
> Closed at: 2023-12-20 22:10:00 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298  

Given:  
```bash  
C:\Users\cmaza\cpp\boost-root>g++ --version  
g++ (MinGW-W64 x86_64-ucrt-posix-seh, built by Brecht Sanders) 12.2.0  
Copyright (C) 2022 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
  
Even a simple file like this will trigger a segfault:  
```cpp  
#include <boost/filesystem.hpp>  
  
namespace fs = boost::filesystem;  
  
int main() {  
    auto fs = fs::unique_path();  
    (void)fs;  
}  
```  
  
For me, the full command is:  
```bash  
C:\Users\cmaza\cpp\boost-root>cls && b2 -q --abbreviate-paths libs/http_proto/test/unit//rawr_ toolset=gcc link=static rtti=off  
```  
  
And the output is:  
```bash  
gcc.link bin.v2\libs\http_proto\test\unit\rawr_.test\gcc-12\dbg\lnk-sttc\rt-off\thrdp-wn32\thrd-mlt\vsblt-hdn\rawr_.exe  
testing.capture-output bin.v2\libs\http_proto\test\unit\rawr_.test\gcc-12\dbg\lnk-sttc\rt-off\thrdp-wn32\thrd-mlt\vsblt-hdn\rawr_.run  
====== BEGIN OUTPUT ======  
  
EXIT STATUS: -1073741819  
====== END OUTPUT ======  
```  
  
Using gdb to get the stacktrace, we see:  
```gdb  
Thread 1 received signal SIGSEGV, Segmentation fault.  
0x00007ffee95981b5 in ?? () from C:\mingw64\bin\libstdc++-6.dll  
(gdb) backtrace  
#0  0x00007ffee95981b5 in ?? () from C:\mingw64\bin\libstdc++-6.dll  
#1  0x00007ffee958f6e0 in ?? () from C:\mingw64\bin\libstdc++-6.dll  
#2  0x00007ff6315472bd in boost::filesystem::path::codecvt () at libs/filesystem/src/path.cpp:1593  
#3  0x00007ff631541ba2 in boost::filesystem::detail::path_traits::convert (from=0x7ff631558817 <boost::winapi::STORE_ERROR_LICENSE_REVOKED_+1479> "%%%%-%%%%-%%%%-%%%%",  
    from_end=0x7ff63155882a <boost::winapi::STORE_ERROR_LICENSE_REVOKED_+1498> "", to=L"", cvt=0x0) at libs/filesystem/src/path_traits.cpp:129  
#4  0x00007ff631551a28 in boost::filesystem::path::assign_op::operator()<char> (this=0x5ffce8, source=0x7ff631558817 <boost::winapi::STORE_ERROR_LICENSE_REVOKED_+1479> "%%%%-%%%%-%%%%-%%%%",  
    source_end=0x7ff63155882a <boost::winapi::STORE_ERROR_LICENSE_REVOKED_+1498> "", cvt=0x0) at ./boost/filesystem/path.hpp:252  
#5  0x00007ff63154f4cb in boost::filesystem::detail::path_traits::dispatch<boost::filesystem::path::assign_op> (cvt=0x0, cb=...,  
    source=0x7ff631558817 <boost::winapi::STORE_ERROR_LICENSE_REVOKED_+1479> "%%%%-%%%%-%%%%-%%%%") at ./boost/filesystem/detail/path_traits.hpp:418  
#6  boost::filesystem::detail::path_traits::dispatch<char [20], boost::filesystem::path::assign_op> (cvt=0x0, cb=..., source=...) at ./boost/filesystem/detail/path_traits.hpp:475  
#7  boost::filesystem::path::assign<char [20]> (this=0x5ffdc0, source=...) at ./boost/filesystem/path.hpp:545  
#8  0x00007ff63154f6cf in boost::filesystem::path::path<char [20], void> (this=0x5ffdc0, source=...) at ./boost/filesystem/path.hpp:364  
#9  0x00007ff63154182f in main () at libs/http_proto/test/unit/rawr.cpp:6  
```  
  
This is trivially replicated for me by just performing:  
```bash  
C:\Users\cmaza\cpp\boost-root>cls && b2 libs/filesystem/test//copy_test toolset=gcc rtti=off  
```  
  
Let me know if there's anything else you'd need on my end to debug this.  
  
Note, this seems to pass fine for `toolset=msvc-14.3` and I'm using a proper version of mingw that links to ucrt as well.

---

## Comment 1

> Username: cmazakas  
> Created at: 2023-12-20 18:22:59 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1864932812  

I have a fork of Filesystem running in appveyor with `rtti=on,off` and the failures even replicate there:  
https://ci.appveyor.com/project/cppalliance/filesystem/builds/48802032

---

## Comment 2

> Username: Lastique  
> Created at: 2023-12-20 20:53:15 UTC  
> Updated at: 2023-12-20 20:57:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865124038  

Without having checked, perhaps `std::locale` on MinGW-w64 requires RTTI to operate? [path.cpp:1593](https://github.com/boostorg/filesystem/blob/e299a19eb51e6ef0e2428acca4a439a07ce0aa99/src/path.cpp#L1593) obtains a `codecvt` facet from a locale, and I'm assuming that this fails. Does something like this work?  
  
```  
int main()  
{  
    std::locale loc;  
    auto const& fac = std::use_facet< std::codecvt< wchar_t, char, std::mbstate_t > >(loc);  
    (void)fac;  
}  
```

---

## Comment 3

> Username: pdimov  
> Created at: 2023-12-20 21:10:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865143058  

I also get crashes on Cygwin, locally. MinGW installed from MSYS2 works, though.

---

## Comment 4

> Username: pdimov  
> Created at: 2023-12-20 21:13:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865146236  

The above program works for me under Cygwin, with or without `-fno-rtti`.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-12-20 21:17:53 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865150993  

However, if I replace `quick.cpp` with  
  
```  
#include <boost/filesystem.hpp>  
#include <boost/core/lightweight_test.hpp>  
  
namespace fs = boost::filesystem;  
  
int main()  
{  
	fs::path p1;  
	p1.codecvt();  
  
    return boost::report_errors();  
}  
```  
it crashes as before.

---

## Comment 6

> Username: pdimov  
> Created at: 2023-12-20 21:24:29 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865157971  

It appears to be a crash in `dynamic_cast`:  
  
```  
C:\boost-git\develop\libs\filesystem>gdb ..\..\bin.v2\libs\filesystem\test\quick.test\gcc-11\debug\link-static\rtti-off\target-os-cygwin\threading-multi\visibility-hidden\quick.exe  
GNU gdb (GDB) (Cygwin 12.1-1) 12.1  
Copyright (C) 2022 Free Software Foundation, Inc.  
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
This is free software: you are free to change and redistribute it.  
There is NO WARRANTY, to the extent permitted by law.  
Type "show copying" and "show warranty" for details.  
This GDB was configured as "x86_64-pc-cygwin".  
Type "show configuration" for configuration details.  
For bug reporting instructions, please see:  
<https://www.gnu.org/software/gdb/bugs/>.  
Find the GDB manual and other documentation resources online at:  
    <http://www.gnu.org/software/gdb/documentation/>.  
  
For help, type "help".  
Type "apropos word" to search for commands related to "word"...  
Reading symbols from ..\..\bin.v2\libs\filesystem\test\quick.test\gcc-11\debug\link-static\rtti-off\target-os-cygwin\threading-multi\visibility-hidden\quick.exe...  
(gdb) run  
Starting program: /cygdrive/c/boost-git/develop/libs/filesystem/..\..\bin.v2\libs\filesystem\test\quick.test\gcc-11\debug\link-static\rtti-off\target-os-cygwin\threading-multi\visibility-hidden\quick.exe  
[New Thread 31420.0x663c]  
[New Thread 31420.0x7574]  
[New Thread 31420.0x74ec]  
[New Thread 31420.0x8dc8]  
  
Thread 1 "quick" received signal SIGSEGV, Segmentation fault.  
0x00000003e828e877 in cygstdc++-6!.dynamic_cast () from /usr/bin/cygstdc++-6.dll  
(gdb) bt  
#0  0x00000003e828e877 in cygstdc++-6!.dynamic_cast () from /usr/bin/cygstdc++-6.dll  
#1  0x00000003e8285af0 in cygstdc++-6!_ZSt9use_facetISt7codecvtIwc10_mbstate_tEERKT_RKSt6locale ()  
   from /usr/bin/cygstdc++-6.dll  
#2  0x0000000100405381 in boost::filesystem::path::codecvt () at ../../libs/filesystem/src/path.cpp:1593  
#3  0x00000001004057c6 in boost::filesystem::detail::path_traits::convert (  
    from=0x100410819 <boost::winapi::STORE_ERROR_LICENSE_REVOKED_+1561> "a",  
    from_end=0x10041081a <boost::winapi::STORE_ERROR_LICENSE_REVOKED_+1562> "", to=..., cvt=0x0)  
    at ../../libs/filesystem/src/path_traits.cpp:129  
#4  0x0000000100409338 in boost::filesystem::path::assign_op::operator()<char> (this=0x7ffffc9e8,  
    source=0x100410819 <boost::winapi::STORE_ERROR_LICENSE_REVOKED_+1561> "a",  
    source_end=0x10041081a <boost::winapi::STORE_ERROR_LICENSE_REVOKED_+1562> "", cvt=0x0)  
    at ..\../boost/filesystem/path.hpp:252  
#5  0x00000001004069a1 in boost::filesystem::detail::path_traits::dispatch<boost::filesystem::path::assign_op> (  
    cvt=0x0, cb=..., source=0x100410819 <boost::winapi::STORE_ERROR_LICENSE_REVOKED_+1561> "a")  
    at ..\../boost/filesystem/detail/path_traits.hpp:418  
#6  boost::filesystem::detail::path_traits::dispatch<char [2], boost::filesystem::path::assign_op> (cvt=0x0, cb=...,  
    source=...) at ..\../boost/filesystem/detail/path_traits.hpp:475  
#7  boost::filesystem::path::assign<char [2]> (this=0x7ffffcaf8, source=...) at ..\../boost/filesystem/path.hpp:545  
#8  0x0000000100406b8f in boost::filesystem::path::path<char [2], void> (this=0x7ffffcaf8, source=...)  
    at ..\../boost/filesystem/path.hpp:364  
#9  0x00000001004010b0 in main () at test/quick.cpp:18  
```

---

## Comment 7

> Username: pdimov  
> Created at: 2023-12-20 21:25:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865158546  

(This is g++ 11.4.0 under Cygwin.)

---

## Comment 8

> Username: Lastique  
> Created at: 2023-12-20 21:29:11 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865165098  

I suspect, my simple test case doesn't fail because RTTI for standard facets are already compiled into the standard library. Boost.Filesystem has its own facet, and if you compile it without RTTI, it is obviously missing, and the `dynamic_cast` from within the standard library cannot possibly work.  
  
I'm inclined to declare this configuration (MinGW or Cygwin without RTTI) unsupported due to the standard library bug, as I don't see a way around it.

---

## Comment 9

> Username: Lastique  
> Created at: 2023-12-20 21:30:17 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865167295  

It might be a good idea to report this to gcc developers.

---

## Comment 10

> Username: pdimov  
> Created at: 2023-12-20 21:31:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865169329  

I can see https://gcc.gnu.org/bugzilla/show_bug.cgi?id=34680 but it's supposedly fixed long ago.  
  
As I said, MSYS2 MinGW (which is GCC 13.2.0) works. So this was either already fixed, or there's something else going on.

---

## Comment 11

> Username: pdimov  
> Created at: 2023-12-20 21:46:16 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865185824  

My WSL (Windows Subsystem for Linux) has g++ 11.4.0 as well, and also crashes on some of the tests, but not all.  
  
```  
pdimov@DESKTOP-707GUMB:~/boost-develop$ ./b2 libs/filesystem/test toolset=gcc rtti=off | grep \.\.\.failed  
...failed testing.capture-output bin.v2/libs/filesystem/test/fstream_test.test/gcc-11/debug/rtti-off/threading-multi/visibility-hidden/fstream_test.run...  
...failed testing.capture-output bin.v2/libs/filesystem/test/cstdio_test.test/gcc-11/debug/rtti-off/threading-multi/visibility-hidden/cstdio_test.run...  
...failed testing.capture-output bin.v2/libs/filesystem/test/path_unit_test_static.test/gcc-11/debug/link-static/rtti-off/threading-multi/visibility-hidden/path_unit_test_static.run...  
...failed testing.capture-output bin.v2/libs/filesystem/test/path_unit_test.test/gcc-11/debug/rtti-off/threading-multi/visibility-hidden/path_unit_test.run...  
...failed testing.capture-output bin.v2/libs/filesystem/test/path_unit_test_v3.test/gcc-11/debug/rtti-off/threading-multi/visibility-hidden/path_unit_test_v3.run...  
...failed updating 5 targets...  
```

---

## Comment 12

> Username: pdimov  
> Created at: 2023-12-20 21:48:14 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865187792  

The output suggests that testing after `imbue` is the culprit.  
  
```  
testing.capture-output bin.v2/libs/filesystem/test/path_unit_test_static.test/gcc-11/debug/link-static/rtti-off/threading-multi/visibility-hidden/path_unit_test_static.run  
====== BEGIN OUTPUT ======  
GNU C++ version 11.4.0, __GXX_EXPERIMENTAL_CXX0X__ defined  
GNU libstdc++ version 20230528  
linux  
Boost version 1.85.0  
Command line: bin.v2/libs/filesystem/test/path_unit_test_static.test/gcc-11/debug/link-static/rtti-off/threading-multi/visibility-hidden/path_unit_test_static  
BOOST_POSIX_API  
BOOST_FILESYSTEM_DECL [no value]  
testing overloads...  
testing constructors...  
testing assignments...  
testing move_construction_and_assignment...  
testing appends...  
testing concats...  
testing modifiers...  
testing observers...  
testing relationals...  
testing inserter and extractor...  
testing other_non_members...  
11100000  
testing iterators...  
testing reverse_iterators...  
testing decompositions...  
testing queries...  
testing imbue locale...  
  imbuing locale ...  
  testing with the imbued locale ...  
Segmentation fault  
  
EXIT STATUS: 139  
====== END OUTPUT ======  
```

---

## Comment 13

> Username: cmazakas  
> Created at: 2023-12-20 22:09:57 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865210821  

Hmm, unfortunately the tests seem to pass for me on Ubuntu 23.10, so who knows what's going on there.  
  
The mystery deepens.

---

## Comment 14

> Username: Lastique  
> Created at: 2023-12-20 22:10:00 UTC  
> Updated at: 2023-12-20 22:11:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865210864  

I have reproduced this on Kubuntu 22.04, gcc 11.4, with this test case:  
  
```  
#include <locale>  
  
class __attribute__((__visibility__("default"))) my_codecvt final :  
    public std::codecvt< wchar_t, char, std::mbstate_t >  
{  
public:  
    explicit my_codecvt(std::size_t refs = 0) :  
        std::codecvt< wchar_t, char, std::mbstate_t >(refs)  
    {  
    }  
      
protected:  
    bool do_always_noconv() const noexcept override { return false; }  
  
    int do_encoding() const noexcept override { return 0; }  
    std::codecvt_base::result do_in(std::mbstate_t&, const char*, const char*, const char*&, wchar_t*, wchar_t*, wchar_t*&) const override { return ok; }  
    std::codecvt_base::result do_out(std::mbstate_t&, const wchar_t*, const wchar_t*, const wchar_t*&, char*, char*, char*&) const override { return ok; }  
    std::codecvt_base::result do_unshift(std::mbstate_t&, char*, char*, char*&) const override { return ok; }  
    int do_length(std::mbstate_t&, const char*, const char*, std::size_t) const override { return 0; }  
    int do_max_length() const noexcept override { return 0; }  
};  
  
int main()  
{  
    std::locale loc(std::locale(), new my_codecvt());  
    auto const& fac = std::use_facet< std::codecvt< wchar_t, char, std::mbstate_t > >(loc);  
    (void)fac;  
}  
```  
  
```  
g++ -std=c++17 -fno-rtti -o locale_no_rtti locale_no_rtti.cpp  
```  
  
```  
#0  0x00007ffff7caccd1 in __dynamic_cast () from /lib/x86_64-linux-gnu/libstdc++.so.6  
#1  0x00007ffff7d5307f in std::codecvt<wchar_t, char, __mbstate_t> const& std::use_facet<std::codecvt<wchar_t, char, __mbstate_t> >(std::locale const&) () from /lib/x86_64-linux-gnu/libstdc++.so.6  
#2  0x000055555555539b in main ()  
```  
  
Clearly the standard library is using `dynamic_cast` when RTTI is disabled. And it does [seem](https://godbolt.org/z/jPG46rcoa) like it was fixed in gcc 13.   
  
I'm going to close this as I don't see a way around this bug.

---

## Comment 15

> Username: cmazakas  
> Created at: 2023-12-20 22:13:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865213568  

I mean, I guess you could add like a `BOOST_PRAGMA_MESSAGE()` or `#error` or something to help people understand this isn't a support configuration.

---

## Comment 16

> Username: Lastique  
> Created at: 2023-12-20 22:14:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865215225  

> Hmm, unfortunately the tests seem to pass for me on Ubuntu 23.10, so who knows what's going on there.  
  
Ubuntu 23.10 [has gcc 13](https://packages.ubuntu.com/search?keywords=g%2B%2B&searchon=names&suite=mantic&section=all).  
  
> I mean, I guess you could add like a `BOOST_PRAGMA_MESSAGE()` or `#error` or something to help people understand this isn't a support configuration.  
  
We typically don't emit errors or warnings due to compiler bugs. Maintaining this would be quite messy, and Boost.Filesystem is not quite easy to maintain as is.

---

## Comment 17

> Username: pdimov  
> Created at: 2023-12-20 22:17:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865217314  

I ran a GHA with rtti=off, https://github.com/boostorg/filesystem/actions/runs/7281186081/job/19841188847  
  
The results are interesting; apparently g++ 11.4.0 on ubuntu-20.04 works, whereas the same g++ 11.4.0 fails for me on ubuntu-22.04. It could be a downstream Ubuntu patch. libc++ seems to work. g++ 12 on ubuntu-22.04 also seems to work.

---

## Comment 18

> Username: pdimov  
> Created at: 2023-12-20 22:31:09 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865230037  

Looks like the commit that fixed it is https://github.com/gcc-mirror/gcc/commit/b3ac43a3c05744d62a963d656bed782fc867ad79, but I have no idea why, as the previous version still had roughly the same `dynamic_cast` logic. Oh well.

---

## Comment 19

> Username: Lastique  
> Created at: 2023-12-20 22:43:21 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865240033  

It looks like the fix was by accident, as the commit simply adds a shortcut for the standard facets. The code is still problematic for user-defined facets (though I would guess, it is a rare beast in real world).  
  
The code did, and still does check for the `__cpp_rtti` macro when the standard library is compiled. I'm pretty sure RTTI is enabled when libstdc++ is compiled, which means it always uses `dynamic_cast` regardless of what the user enables when he builds his code.

---

## Comment 20

> Username: pdimov  
> Created at: 2023-12-20 22:51:40 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865246447  

But the function is a template, so it can't be compiled when Facet is user-defined.

---

## Comment 21

> Username: Lastique  
> Created at: 2023-12-20 23:02:07 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865254488  

You're right, they explicitly instantiate this (and other) templates for standard facets in the `locale-inst.cc` file. This means they "only" have an ODR violation, which works out in practice, since the code doesn't reach the `static_cast`/`dynamic_cast` distinction for the standard facets. Still, it doesn't look like this was an intentional fix.

---

## Comment 22

> Username: Lastique  
> Created at: 2023-12-20 23:45:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865283540  

For reference, I have created a [gcc bug](https://gcc.gnu.org/bugzilla/show_bug.cgi?id=113099).

---

## Comment 23

> Username: cmazakas  
> Created at: 2023-12-20 23:54:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/298#issuecomment-1865289072  

Thank you, Andrey. I love it when bug hunts like these actually have tangible payoffs in the real world.
