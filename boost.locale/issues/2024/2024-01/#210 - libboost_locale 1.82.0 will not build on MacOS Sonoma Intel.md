# #210 - libboost_locale 1.82.0 will not build on MacOS Sonoma Intel [Closed]

> Username: olekrisek  
> Created at: 2024-01-03 17:28:14 UTC  
> Updated at: 2024-02-06 18:29:20 UTC  
> Closed at: 2024-02-06 18:29:20 UTC  
> Url: https://github.com/boostorg/locale/issues/210  

Hi, I am trying to build the boost libraries. Everything goes fine, except for the libboost_locale that that does not got built.   
The build goes fine for 1.79.0, but my project uses features in 1.82, so I cannot switch.   
  
I am using this setup to build:   
```  
#!/bin/sh  
  
rm -rf arm64 x86_64 universal stage bin.v2  
rm -f b2 project-config*  
export MACOSX_DEPLOYMENT_TARGET=12.3  
./bootstrap.sh cxxflags="-arch x86_64 -arch arm64" cflags="-arch x86_64 -arch arm64" linkflags="-arch x86_64 -arch arm64"  
./b2 toolset=clang-darwin target-os=darwin architecture=arm abi=aapcs cxxflags="-arch arm64" cflags="-arch arm64" linkflags="-arch arm64" -a  
mkdir -p arm64a && cp stage/lib/*.a arm64a  
mkdir -p arm64 && cp stage/lib/*.dylib arm64  
export MACOSX_DEPLOYMENT_TARGET=10.13  
./b2 toolset=clang-darwin target-os=darwin architecture=x86 cxxflags="-arch x86_64" cflags="-arch x86_64" linkflags="-arch x86_64" abi=sysv binary-format=mach-o -a  
mkdir x86_64 && cp stage/lib/*.dylib x86_64  
cp stage/lib/*.a x86_64  
mkdir universal  
for dylib in arm64/*; do   
  lipo -create -arch arm64 $dylib -arch x86_64 x86_64/$(basename $dylib) -output universal/$(basename $dylib);   
done  
  
mkdir universal_a  
for dylib in arm64a/*; do   
  lipo -create -arch arm64 $dylib -arch x86_64 x86_64/$(basename $dylib) -output universal_a/$(basename $dylib);   
done  
  
for dylib in universal/*; do  
  lipo $dylib -info;  
done  
  
for dylib in universal_a/*; do  
  lipo $dylib -info;  
done  
```  
  
the config.log file looks like this:   
[config.log](https://github.com/boostorg/locale/files/13821724/config.log)  
  
The output (terminal) looks like this:   
[boost.1.8.2 build.txt](https://github.com/boostorg/locale/files/13821740/boost.1.8.2.build.txt)

---

## Comment 1

> Username: Flamefire  
> Created at: 2024-01-04 08:32:55 UTC  
> Url: https://github.com/boostorg/locale/issues/210#issuecomment-1876701364  

Thanks for the output which shows the issue: It seems to be the same as https://github.com/boostorg/locale/issues/209 in that your compiler doesn't support C++11 by default or Boost.Config fails to detect it.  
  
> libs/config/checks/test_case.cpp:986:7: error: "Defect macro BOOST_NO_CXX11_STATIC_ASSERT is defined."  
  
You can try passing e.g. `cxxstd=11` to b2

---

## Comment 2

> Username: olekrisek  
> Created at: 2024-01-04 10:00:52 UTC  
> Url: https://github.com/boostorg/locale/issues/210#issuecomment-1876819426  

I did, and it seems to work:   
`Architectures in the fat file: universal_a/libboost_locale.a are: x86_64 arm64`  
  
However, when I try to use it, this snippet crashes the application:   
```  
// Initialize boost::locale  
    boost::locale::generator gen;  
    std::locale loc = gen("");  
    const boost::locale::collator<char>& coll = std::use_facet<boost::locale::collator<char>>(loc);  
      
    auto result = coll.compare(boost::locale::collate_level::secondary, str1, str2);  
    if (result < 0) return -1;  
    if (result > 0) return 1;  
    return 0;  
```  
In the file: `collator.hpp` - see the screenshots from Xcode debugger.   
![Skjermbilde 2024-01-04 kl  10 56 40](https://github.com/boostorg/locale/assets/3278226/58115273-2262-491c-a109-48af29db38db)  
![Skjermbilde 2024-01-04 kl  10 57 32](https://github.com/boostorg/locale/assets/3278226/e67d9126-7a71-4eda-8242-e6077d2975f0)  
![Skjermbilde 2024-01-04 kl  10 57 20](https://github.com/boostorg/locale/assets/3278226/66d0c809-f926-4298-9d45-934906f87a0a)  
![Skjermbilde 2024-01-04 kl  10 57 01](https://github.com/boostorg/locale/assets/3278226/834d97d3-0ce8-48ca-9f89-7c083f001fc9)

---

## Comment 3

> Username: Flamefire  
> Created at: 2024-01-04 10:17:48 UTC  
> Url: https://github.com/boostorg/locale/issues/210#issuecomment-1876844603  

Glad the build worked for you.  
  
As for the error: This doesn't show up in the test suite. Where exactly does this happen? `do_compare` is a virtual function.  
  
- Does it succeed in the l/r.data/size calls?  
- Does it enter the `do_compare` implementation? Which one?  
- Is your application using the correct library/architecture?  
- Does it work "plainly", i.e. without the `lipo` stuff?  
  
Especially the use of different `MACOSX_DEPLOYMENT_TARGET`s and other b2 flags make me suspect an ODR violation at some point.

---

## Comment 4

> Username: olekrisek  
> Created at: 2024-01-04 11:54:55 UTC  
> Updated at: 2024-01-04 12:28:10 UTC  
> Url: https://github.com/boostorg/locale/issues/210#issuecomment-1876974270  

Maybe I have to build with debug mode to follow into `do_compare` - I can put a breakpoint on it, and the crash does not occur before I try to enter into it. The only thing I see, is the call stack in Xcode:   
![Skjermbilde 2024-01-04 kl  12 52 59](https://github.com/boostorg/locale/assets/3278226/b92f8eac-eaea-47f0-bfbe-0fb24da321cc)  
  
The differences in deployment target for intel and arm is a project requirement, as arm first become available with 12.3, bu t many users still use earlier OS on intel platform. As those are separately built, I did not knew this could be a problem. When I compile the main project in debug mode, only the intel part is linked.   
  
The l/r data and size, it seems this is OK:   
![Skjermbilde 2024-01-04 kl  13 26 45](https://github.com/boostorg/locale/assets/3278226/5656364d-01af-4f9f-b27f-337731be9341)

---

## Comment 5

> Username: Flamefire  
> Created at: 2024-01-04 13:27:46 UTC  
> Url: https://github.com/boostorg/locale/issues/210#issuecomment-1877095135  

It looks like the virtual function dispatch gets messed up in your use case as indicated by `__cxxabiv1::__si_class_type_info` in the call stack which doesn't look right. This could have multiple causes:  
  
- Error in your toolchain (i.e. compiler, linker, etc)  
- memory corruption happening somewhere  
- ODR violations such as due to your usage of `lipo` to stuff those libs together. Hence the suggestion to try without that first  
  
Maybe it is also a visibility related issue. Can you (additonally) try chainging https://github.com/boostorg/locale/blob/ab989963dd57967a1547c50964a191e6243f6bb9/include/boost/locale/collator.hpp#L49  
to `    class BOOST_SYMBOL_VISIBLE collator : public std::collate<CharType> {`  
  
This might help the linker getting the vtable right.

---

## Comment 6

> Username: olekrisek  
> Created at: 2024-01-04 16:14:35 UTC  
> Updated at: 2024-01-04 17:16:36 UTC  
> Url: https://github.com/boostorg/locale/issues/210#issuecomment-1877368286  

Hi,   
Now I have done this:   
- Removed the ARM part of the lib compilation.   
- Using only `export MACOSX_DEPLOYMENT_TARGET=12.3`  
- Done the changes to `class collator` as described. Both for the include headers in my project, and in the boost folder with all the header files.   
- recompiled the boost libraries. Just replaced the `boost_locale.a` in my project, with this new intel-only version.   
- Cleaned build folder for my project  
- Done a recompile for the project.   
- Copied the product into the plugin folder for the target application.   
- Restarted FileMaker as the host application, and tried to run the functions again.   
- FileMaker crash:   
```  
Thread 0 Crashed::  Dispatch queue: com.apple.main-thread  
0   ???                           	    0x7ff842605f08 vtable for __cxxabiv1::__si_class_type_info + 16  
1   ACF_plugin_db                 	       0x11b279c49 boost::locale::collator<char>::compare(boost::locale::collate_level, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&) const + 153 (collator.hpp:98)  
2   ACF_plugin_db                 	       0x11b279a4f utf8CompareOptimized(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&) + 463 (runtime.cpp:163)  
3   ACF_plugin_db                 	       0x11b279d5d ACF_Runtime::compareRows(int, int, std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&) + 269 (runtime.cpp:182)  
4   ACF_plugin_db                 	       0x11b2f276c ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0::operator()(int, int) const + 44 (runtime.cpp:248)  
5   ACF_plugin_db                 	       0x11b2f2ad1 unsigned int std::__1::__sort3[abi:v160006]<std::__1::_ClassicAlgPolicy, ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0&, int*>(int*, int*, int*, ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0&) + 81 (sort.h:97)  
6   ACF_plugin_db                 	       0x11b2f27dd std::__1::enable_if<!__use_branchless_sort<ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0&, int*>::value, void>::type std::__1::__sort3_maybe_branchless[abi:v160006]<std::__1::_ClassicAlgPolicy, ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0&, int*>(int*, int*, int*, ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0&) + 45 (sort.h:250)  
7   ACF_plugin_db                 	       0x11b2f28b9 void std::__1::__insertion_sort_3[abi:v160006]<std::__1::_ClassicAlgPolicy, ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0&, int*>(int*, int*, ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0&) + 57 (sort.h:329)  
8   ACF_plugin_db                 	       0x11b2f21cc void std::__1::__introsort<std::__1::_ClassicAlgPolicy, ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0&, int*>(int*, int*, ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0&, std::__1::iterator_traits<int*>::difference_type) + 332 (sort.h:465)  
9   ACF_plugin_db                 	       0x11b2f1fe2 void std::__1::__sort<ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0&, int*>(int*, int*, ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0&) + 82 (sort.h:639)  
10  ACF_plugin_db                 	       0x11b2f1f77 void std::__1::__sort_impl[abi:v160006]<std::__1::_ClassicAlgPolicy, std::__1::__wrap_iter<int*>, ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0>(std::__1::__wrap_iter<int*>, std::__1::__wrap_iter<int*>, ACF_Runtime::multisort_arrays(std::__1::vector<int, std::__1::allocator<int>> const&, std::__1::vector<int, std::__1::allocator<int>> const&)::$_0&) +   
```  
  
Here is the updated boost compile script:   
```  
rm -rf arm64 x86_64 universal stage bin.v2  
rm -f b2 project-config*  
export MACOSX_DEPLOYMENT_TARGET=12.3  
./bootstrap.sh cxxflags="-arch x86_64 -arch arm64" cflags="-arch x86_64 -arch arm64" linkflags="-arch x86_64 -arch arm64"  
#./b2 variant=debug toolset=clang-darwin target-os=darwin architecture=arm abi=aapcs cxxstd=11 cxxflags="-arch arm64" cflags="-arch arm64" linkflags="-arch arm64" -a  
#mkdir -p arm64a && cp stage/lib/*.a arm64a  
#mkdir -p arm64 && cp stage/lib/*.dylib arm64  
#export MACOSX_DEPLOYMENT_TARGET=10.13  
./b2 toolset=clang-darwin target-os=darwin architecture=x86 cxxstd=11 cxxflags="-arch x86_64" cflags="-arch x86_64" linkflags="-arch x86_64" abi=sysv binary-format=mach-o -a  
mkdir x86_64 && cp stage/lib/*.dylib x86_64  
cp stage/lib/*.a x86_64  
#mkdir universal  
#for dylib in arm64/*; do   
#  lipo -create -arch arm64 $dylib -arch x86_64 x86_64/$(basename $dylib) -output universal/$(basename $dylib);   
#done  
  
#mkdir universal_a  
#for dylib in arm64a/*; do   
#  lipo -create -arch arm64 $dylib -arch x86_64 x86_64/$(basename $dylib) -output universal_a/$(basename $dylib);   
#done  
  
#for dylib in universal/*; do  
#  lipo $dylib -info;  
#done  
  
#for dylib in universal_a/*; do  
#  lipo $dylib -info;  
#done  
```  
The Info for the lib now says:   
```  
file libboost_locale.a   
libboost_locale.a: current ar archive  
ole@Oles-Mac-mini Xcode % lipo libboost_locale.a -info  
Non-fat file: libboost_locale.a is architecture: x86_64  
ole@Oles-Mac-mini Xcode %   
```  
  
**UPDATE**  
I also see there there is some errors in the config.log - around locale build:   
  
```  
clang-darwin.compile.c++ bin.v2/libs/locale/build/clang-darwin-15/release/cxxstd-11-iso/threading-multi/visibility-hidden/has_iconv.o  
clang-darwin.link bin.v2/libs/locale/build/clang-darwin-15/release/cxxstd-11-iso/threading-multi/visibility-hidden/has_iconv  
ld: Undefined symbols:  
  _iconv_open, referenced from:  
      _main in has_iconv.o  
clang: error: linker command failed with exit code 1 (use -v to see invocation)  
  
    "clang++" -arch x86_64  -o "bin.v2/libs/locale/build/clang-darwin-15/release/cxxstd-11-iso/threading-multi/visibility-hidden/has_iconv" "bin.v2/libs/locale/build/clang-darwin-15/release/cxxstd-11-iso/threading-multi/visibility-hidden/has_iconv.o"        -fPIC -m64 -std=c++11 -fvisibility=hidden -fvisibility-inlines-hidden --target=x86_64-apple-darwin  
  
...failed clang-darwin.link bin.v2/libs/locale/build/clang-darwin-15/release/cxxstd-11-iso/threading-multi/visibility-hidden/has_iconv...  
...failed updating 1 target...  
...updated 8 targets...  
...found 2 targets...  
...updating 2 targets...  
clang-darwin.compile.c++ bin.v2/libs/locale/build/clang-darwin-15/release/cxxstd-11-iso/threading-multi/visibility-hidden/has_external_iconv_obj.o  
clang-darwin.link bin.v2/libs/locale/build/clang-darwin-15/release/cxxstd-11-iso/threading-multi/visibility-hidden/has_external_iconv  
...updated 2 targets...  
...found 4 targets...  
...updating 2 targets...  
clang-darwin.compile.c++ bin.v2/libs/locale/build/clang-darwin-15/release/cxxstd-11-iso/threading-multi/visibility-hidden/has_icu_test.o  
/Users/ole/Prosjekter/Boost/boost_1_82_0/libs/locale/build/has_icu_test.cpp:6:10: fatal error: 'unicode/coll.h' file not found  
#include <unicode/coll.h>  
         ^~~~~~~~~~~~~~~~  
1 error generated.  
  
    "clang++" -x c++ -std=c++11 -fvisibility-inlines-hidden -fPIC -m64 -O3 -Wall -fvisibility=hidden -Wno-inline --target=x86_64-apple-darwin -arch x86_64 -arch x86_64 -DBOOST_ALL_NO_LIB=1 -DNDEBUG -I"."  -c -o "bin.v2/libs/locale/build/clang-darwin-15/release/cxxstd-11-iso/threading-multi/visibility-hidden/has_icu_test.o" "/Users/ole/Prosjekter/Boost/boost_1_82_0/libs/locale/build/has_icu_test.cpp"  
  
...failed clang-darwin.compile.c++ bin.v2/libs/locale/build/clang-darwin-15/release/cxxstd-11-iso/threading-multi/visibility-hidden/has_icu_test.o...  
...skipped <pbin.v2/libs/locale/build/clang-darwin-15/release/cxxstd-11-iso/threading-multi/visibility-hidden>has_icu for lack of <pbin.v2/libs/locale/build/clang-darwin-15/release/cxxstd-11-iso/threading-multi/visibility-hidden>has_icu_test.o...  
...failed updating 1 target...  
...skipped 1 target...  
...found 1 target...  
...updating 1 target...  
```  
  
can this `ld: Undefined symbols:  
  _iconv_open, referenced from:  
      _main in has_iconv.o`   
have something to do with this article I found:   
https://stackoverflow.com/questions/57734434/libiconv-or-iconv-undefined-symbol-on-mac-osx/57734435

---

## Comment 7

> Username: Flamefire  
> Created at: 2024-01-04 17:53:48 UTC  
> Url: https://github.com/boostorg/locale/issues/210#issuecomment-1877525665  

Thank you for the tests!  
  
After excluding all impossible things what remains must be the truth: There is a bug in Boost.Locale which has been there since forever.  
  
But slow: The `config.log` message about Iconv is fine. The failure you see is basically a configure check and corresponds to this:  
```  
    - iconv (libc)             : no [2]  
    - iconv (separate)         : yes [2]  
```  
  
However The other one is not! It checks for ICU and fails to find it: `- icu                      : no [2]`  
  
Not sure if your debugger allows this but check the actual type of `boost::locale::collator<char>`, i.e. of `*this` inside the `compare` call. It is likely one from `boost::locale::impl_std` or `boost::locale::impl_posix`. And those only derive from `std::collate` but not from `boost::locale::collate` which means they do not implement that `do_compare` function taking a `level` argument. And seemingly `std::use_facet` does not check the actual type but only the id.     
In particular only the WinAPI and the ICU backends implement that facet and you don't even have either build which explains this.  
  
So in a semi-correct version your use of `std::use_facet<boost::locale::collator<char>>` should throw a `std::bad_cast`. At least until the Posix and Std backends implement that facet which I don't think is possible.  
  
For you this means that you need to build Boost.Locale with ICU to get this working.

---

## Comment 8

> Username: olekrisek  
> Created at: 2024-01-04 19:01:24 UTC  
> Url: https://github.com/boostorg/locale/issues/210#issuecomment-1877611527  

Here is the breakdown of `*this`  
  
![Skjermbilde 2024-01-04 kl  19 53 14](https://github.com/boostorg/locale/assets/3278226/9b5f4293-c208-46a2-b811-63bf1756b833)  
  
I know there is some libicucore.tbd in standard macOS. Would it be possible to use that? Like to avoid embedding a huge library into this plugin ( that is at least as the size of the plugin itself only to be able to compare UTF8 strings in a sorting algorithm.   
![Skjermbilde 2024-01-04 kl  19 58 28](https://github.com/boostorg/locale/assets/3278226/b66bef83-3c79-4f98-9dbc-a63da890d300)

---

## Comment 9

> Username: Flamefire  
> Created at: 2024-01-04 20:00:06 UTC  
> Url: https://github.com/boostorg/locale/issues/210#issuecomment-1877684631  

Hm, it doesn't seem to read the actual type data.  
  
> I know there is some libicucore.tbd in standard macOS. Would it be possible to use that? Like to avoid embedding a huge library into this plugin ( that is at least as the size of the plugin itself only to be able to compare UTF8 strings in a sorting algorithm.  
  
I don't know as I'm not familiar with how macOS frameworks work exactly and if/how b2 supports them. Check if you can find `<x>/include/unicode/coll.h` somewhere and use that `-s ICU_PATH=x` you might also need to set  ICU_ICUUC_NAME,  ICU_ICUDT_NAME and ICU_ICUIN_NAME via `-s`, potentially to `libicucore.tbd` or so. But that's only random guessing.  
  
You could get a bit further without that by using `std::collate<wchar_t>` which is implemented for the posix backend and converting your input to that first via `boost::locale::to_utf<wchar_t>` or `boost::locale::utf_to_utf<wchar_t>` if you know your input is UTF-8. However that doesn't let you specify a collation level. For your use case you might want to apply case folding first.  
  
Further checking: It looks like you could also use the std backend which implements a `std::collate<char>` facet that does the UTF-8 to wide comparison by default. Again you might need to apply case folding first before feeding your strings to collate.  
  
Those 2 approaches get you close but not to the same level as what ICU provides as secondary collation rules include not only case folding but also punctuations/accents and such.

---

## Comment 10

> Username: olekrisek  
> Created at: 2024-01-04 21:14:23 UTC  
> Updated at: 2024-01-04 21:18:28 UTC  
> Url: https://github.com/boostorg/locale/issues/210#issuecomment-1877768706  

Thank you for feedback and tips around this.   
  
You are right, I have built the ICU libs for intel/arm now, and seems to get it detected.   
The   
https://github.com/apotocki/icu4c-iosx  
did the trick. Just had to adjust the build for the deployment targets.   
  
I have put those lines into the build script:   
```  
export ICU_PATH=/Users/ole/Prosjekter/icu/icu4c-iosx/product  
export LD_LIBRARY_PATH=$ICU_PATH/lib:$LD_LIBRARY_PATH  
export CPLUS_INCLUDE_PATH=$ICU_PATH/include/unicode:$CPLUS_INCLUDE_PATH  
```  
and then `-sICU_PATH=$ICU_PATH` on the `b2` lines.   
  
It seems like boost_regex also uses ICU, so this can probably solve som issues with regex and UTF8 chars.   
  
I will let you know how it goes.

---

## Comment 11

> Username: Flamefire  
> Created at: 2024-02-06 18:29:20 UTC  
> Url: https://github.com/boostorg/locale/issues/210#issuecomment-1930527000  

Closing this as the initial issue has been solved by passing `cxxstd=11` to b2.  
  
Tracking the type confusion regarding `boost::locale::collator` in #215  
  
Feel free to open a new issue if there are other problems.
