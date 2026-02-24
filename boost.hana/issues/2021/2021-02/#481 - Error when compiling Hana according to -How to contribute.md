# #481 - Error when compiling Hana according to "How to contribute" [Open]

> Username: Aster89  
> Created at: 2021-02-11 08:01:48 UTC  
> Updated at: 2021-10-28 05:18:34 UTC  
> Url: https://github.com/boostorg/hana/issues/481  

I've followed steps 1, 2, and 4 (not 3) from [How to contribute](https://github.com/boostorg/hana/blob/master/CONTRIBUTING.md), but I get this error:  
```  
Scanning dependencies of target test.string.zero  
[  2%] Building CXX object test/CMakeFiles/test.string.zero.dir/string/zero.cpp.o  
In file included from /home/enrico/hana/include/boost/hana/string.hpp:13,  
                 from /home/enrico/hana/test/string/zero.cpp:7:  
/home/enrico/hana/include/boost/hana/fwd/string.hpp:268:24: error: ISO C++ did not adopt string literal operator templates taking an argument pack of characters [-Werror=pedantic]  
  268 |         constexpr auto operator"" _s();  
      |                        ^~~~~~~~  
In file included from /home/enrico/hana/test/string/zero.cpp:7:  
/home/enrico/hana/include/boost/hana/string.hpp:117:24: error: ISO C++ did not adopt string literal operator templates taking an argument pack of characters [-Werror=pedantic]  
  117 |         constexpr auto operator"" _s() {  
      |                        ^~~~~~~~  
cc1plus: note: unrecognized command-line option ‘-Wno-self-assign-overloaded’ may have been intended to silence earlier diagnostics  
cc1plus: all warnings being treated as errors  
make[3]: *** [test/CMakeFiles/test.string.zero.dir/build.make:82: test/CMakeFiles/test.string.zero.dir/string/zero.cpp.o] Error 1  
make[2]: *** [CMakeFiles/Makefile2:13422: test/CMakeFiles/test.string.zero.dir/all] Error 2  
make[1]: *** [CMakeFiles/Makefile2:1622: CMakeFiles/check.dir/rule] Error 2  
make: *** [Makefile:337: check] Error 2  
```  
My compiler is  
```  
$ g++ --version   
g++ (GCC) 10.2.0  
Copyright (C) 2020 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
```  
which looks ok, based on [General notes on compiler support](https://github.com/boostorg/hana/wiki/General-notes-on-compiler-support).

---

## Comment 1

> Username: ricejasonf  
> Created at: 2021-10-25 18:07:15 UTC  
> Updated at: 2021-10-25 18:12:06 UTC  
> Url: https://github.com/boostorg/hana/issues/481#issuecomment-951174006  

The compile-time string user defined literal is a GNU extension. It appears the `-pedantic` flag is causing this to be an error.  
  
`BOOST_HANA_CONFIG_ENABLE_STRING_UDL` should probably not be set if `BOOST_HANA_HAS_PEDANTIC` is set.  
  
Are you manually defining either of those?  
  
EDIT: The input cli command would be helpful (via `VERBOSE=1 make test.string.zero`).

---

## Comment 2

> Username: Aster89  
> Created at: 2021-10-25 20:02:02 UTC  
> Url: https://github.com/boostorg/hana/issues/481#issuecomment-951262742  

No, I'm not setting those. Both the commands below print `does not exist` in Bash:  
```bash  
echo ${BOOST_HANA_HAS_PEDANTIC-does not exist}  
echo ${BOOST_HANA_CONFIG_ENABLE_STRING_UDL-does not exist}  
```  
Here's the output of `VERBOSE=1 make test.string.zero`:  
```none  
/usr/bin/cmake -S/home/enrico/hana -B/home/enrico/hana/build --check-build-system CMakeFiles/Makefile.cmake 0  
make  -f CMakeFiles/Makefile2 test.string.zero  
make[1]: Entering directory '/home/enrico/hana/build'  
/usr/bin/cmake -S/home/enrico/hana -B/home/enrico/hana/build --check-build-system CMakeFiles/Makefile.cmake 0  
/usr/bin/cmake -E cmake_progress_start /home/enrico/hana/build/CMakeFiles 0  
make  -f CMakeFiles/Makefile2 test/CMakeFiles/test.string.zero.dir/all  
make[2]: Entering directory '/home/enrico/hana/build'  
make  -f test/CMakeFiles/test.string.zero.dir/build.make test/CMakeFiles/test.string.zero.dir/depend  
make[3]: Entering directory '/home/enrico/hana/build'  
cd /home/enrico/hana/build && /usr/bin/cmake -E cmake_depends "Unix Makefiles" /home/enrico/hana /home/enrico/hana/test /home/enrico/hana/build /home/enrico/hana/build/test /home/enrico/hana/build/test/CMakeFiles/test.string.zero.dir/DependInfo.cmake --color=  
Dependencies file "test/CMakeFiles/test.string.zero.dir/string/zero.cpp.o.d" is newer than depends file "/home/enrico/hana/build/test/CMakeFiles/test.string.zero.dir/compiler_depend.internal".  
Consolidate compiler generated dependencies of target test.string.zero  
make[3]: Leaving directory '/home/enrico/hana/build'  
make  -f test/CMakeFiles/test.string.zero.dir/build.make test/CMakeFiles/test.string.zero.dir/build  
make[3]: Entering directory '/home/enrico/hana/build'  
Building CXX object test/CMakeFiles/test.string.zero.dir/string/zero.cpp.o  
cd /home/enrico/hana/build/test && /usr/bin/c++ -DBOOST_HANA_CONFIG_ENABLE_STRING_UDL -I/home/enrico/hana/test/_include -I/home/enrico/hana/include -fdiagnostics-color -ftemplate-backtrace-limit=0 -pedantic -Wall -Werror -Wextra -Wno-self-assign-overloaded -Wno-unused-local-typedefs -Wwrite-strings -MD -MT test/CMakeFiles/test.string.zero.dir/string/zero.cpp.o -MF CMakeFiles/test.string.zero.dir/string/zero.cpp.o.d -o CMakeFiles/test.string.zero.dir/string/zero.cpp.o -c /home/enrico/hana/test/string/zero.cpp  
In file included from /home/enrico/hana/include/boost/hana/string.hpp:13,  
                 from /home/enrico/hana/test/string/zero.cpp:7:  
/home/enrico/hana/include/boost/hana/fwd/string.hpp:268:24: error: ISO C++ did not adopt string literal operator templates taking an argument pack of characters [-Werror=pedantic]  
  268 |         constexpr auto operator"" _s();  
      |                        ^~~~~~~~  
In file included from /home/enrico/hana/test/string/zero.cpp:7:  
/home/enrico/hana/include/boost/hana/string.hpp:117:24: error: ISO C++ did not adopt string literal operator templates taking an argument pack of characters [-Werror=pedantic]  
  117 |         constexpr auto operator"" _s() {  
      |                        ^~~~~~~~  
cc1plus: note: unrecognized command-line option ‘-Wno-self-assign-overloaded’ may have been intended to silence earlier diagnostics  
cc1plus: all warnings being treated as errors  
make[3]: *** [test/CMakeFiles/test.string.zero.dir/build.make:76: test/CMakeFiles/test.string.zero.dir/string/zero.cpp.o] Error 1  
make[3]: Leaving directory '/home/enrico/hana/build'  
make[2]: *** [CMakeFiles/Makefile2:12963: test/CMakeFiles/test.string.zero.dir/all] Error 2  
make[2]: Leaving directory '/home/enrico/hana/build'  
make[1]: *** [CMakeFiles/Makefile2:12970: test/CMakeFiles/test.string.zero.dir/rule] Error 2  
make[1]: Leaving directory '/home/enrico/hana/build'  
make: *** [Makefile:5834: test.string.zero] Error 2  
```

---

## Comment 3

> Username: ricejasonf  
> Created at: 2021-10-25 21:11:11 UTC  
> Updated at: 2021-10-25 21:12:16 UTC  
> Url: https://github.com/boostorg/hana/issues/481#issuecomment-951334678  

I guess GCC just changed their policy, and we are not up to date with that.  
  
For the tests there is only `test.string.udl` that actually requires `BOOST_HANA_CONFIG_ENABLE_STRING_UDL`.  
  
We should disable it by default and add it only for the pertinent test/examples. Looking at `tests/CMakeLists.txt` there is an example of handling a set of tests differently. `github_75` is added to the list of excluded tests and then has special rules applied to it. We should do something similar for `test.string.udl`, perhaps having the `boost_hana_set_test_properties` cmake function handle tests that require it such that `-pedantic` is *not* set only when necessary.

---

## Comment 4

> Username: Aster89  
> Created at: 2021-10-28 05:18:34 UTC  
> Url: https://github.com/boostorg/hana/issues/481#issuecomment-953510984  

And how do I go about it? Sorry, but I know very little about makefiles and build systems :cry:
