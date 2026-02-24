# #990 - Test with more modern compilers & standards [Open]

> Username: h-vetinari  
> Created at: 2024-12-12 01:48:40 UTC  
> Updated at: 2025-08-30 11:29:23 UTC  
> Url: https://github.com/boostorg/boost/issues/990  

Congratulations to the 1.87 release! 🥳   
  
Looking at the [release notes](https://www.boost.org/users/history/version_1_87_x.html), I see under "Boost's primary test compilers are:"  
```  
Linux:  
    Clang, C++03: 3.4, 3.5, 3.6, 3.7, 3.8, 3.9, 12.0.0, 13.0.0, 14.0.0, 15.0.0  
    Clang, C++11: 3.4, 11.0.0, 13.0.0, 14.0.0, 15.0.0  
    Clang, C++14: 3.5, 3.6, 3.7, 3.8, 3.9, 4.0, 5.0, 12.0.0, 13.0.0, 14.0.0, 15.0.0  
    Clang, C++17: 6.0.1, 7.0.0, 8.0.0, 9.0.0, 10.0.0, 11.0.0, 12.0.0, 13.0.0, 14.0.0, 15.0.0  
    Clang, C++20: 11.0.0, 12.0.0, 13.0.0, 14.0.0, 15.0.0  
    GCC, C++03: 4.6.3, 11, 12  
    GCC, C++11: 4.7.3, 4.8.5, 11, 12  
    GCC, C++14: 5.4.0, 6.4.0, 7.3.0, 8.0.1, 9.1.0, 11, 12  
    GCC, C++17: 7.3.0, 8.0.1, 9.1.0, 11, 12  
    GCC, C++20: 8.0.1, 9.1.0, 10, 11, 12  
```  
This is almost 2.5 years behind current compilers on linux (GCC 12 [released](https://github.com/gcc-mirror/gcc/releases/tag/releases%2Fgcc-12.1.0) in May 2022,  GCC 14 [in](https://github.com/gcc-mirror/gcc/releases/tag/releases%2Fgcc-14.1.0) May 2024; Clang 15 [released](https://github.com/llvm/llvm-project/releases/tag/llvmorg-15.0.0) in Sept. 2022, Clang 19 [in](https://github.com/llvm/llvm-project/releases/tag/llvmorg-19.1.0) Sept. 2024).  
  
I don't know if this is just the documentation that's out of date, or the actual testing, but in either case it should be updated. Compilers regularly add tighter warnings (or even error-by-default) on various aspects of the language, and IMO it should be expected of one of the most prominent C++ libraries to be compatible with current compilers upon release.   
  
Note, I'm not advocating to test the _entire_ version range. For example, I hardly think that testing Clang 3.5, 3.6, 3.7, 3.8, 3.9 (in addition to 3.4) has any benefits in 2025. I leave the question of whether resp. how to thin out the list to the maintainers here, but the newer end should be supported.  
  
An example where users ran into this: https://github.com/boostorg/boost/issues/857 (though probably many more; haven't searched subproject issue trackers).

---

## Comment 1

> Username: h-vetinari  
> Created at: 2024-12-12 01:49:15 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-2537570463  

Ah, and obviously C++23 & C++26 should be tested as well.

---

## Comment 2

> Username: MarDiehl  
> Created at: 2025-02-21 21:36:21 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-2675595065  

It seems that the GCC 12 test for charconv is actually using GCC 11: https://github.com/boostorg/charconv/actions/runs/13462805806/job/37621903734#step:6:98

---

## Comment 3

> Username: h-vetinari  
> Created at: 2025-08-19 06:45:40 UTC  
> Updated at: 2025-08-19 06:52:33 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3199445570  

building boost 1.88 using the b2-bootstrap is broken with clang 19 on osx (clang 18 works), while [boost 1.89](https://github.com/conda-forge/boost-feedstock/pull/236) is broken[^1] with all of: clang 18, 19 and 20.  
  
[^1]: I've rebased out the commits that tried 18, 19, 20, but the logs are still there for about a month  
  
Going all the way back to clang 17 (almost 2 years old by now) "fixes" things, but this is a bit ridiculous TBH. I understand how difficult open source maintenance is (check my GH profile), but in an age of godbolt and various other ways to easily get pre-built and up-to-date toolchains, there really isn't a reason for not testing with up-to-date compilers.  
  
As one of several options: I spend a substantial amount of effort in keeping the clang and GCC toolchains in [conda-forge](https://conda-forge.org) up-to-date (including [prereleases](https://github.com/conda-forge/clang-compiler-activation-feedstock/pull/168) for LLVM); it would be a breeze to set up environments with GCC 12, 13, 14, 15 on linux (and windows, if desired) resp. clang 17, 18, 19, 20 (on linux, osx and windows) and run compile-tests against the boost master branch.  
  
<details>  
<summary>boost 1.89 & clang 18</summary>  
  
```  
+ CFLAGS='-march=core2 -mssse3 -ftree-vectorize -fPIC -fstack-protector-strong -O2 -pipe -isystem $PREFIX/include -fdebug-prefix-map=/Users/runner/miniforge3/conda-bld/boost-split_1755173949834/work=/usr/local/src/conda/boost-split-1.89.0 -fdebug-prefix-map=$PREFIX=/usr/local/src/conda-prefix'  
+ CXX=$BUILD_PREFIX/bin/x86_64-apple-darwin13.4.0-clang++  
+ CC=$BUILD_PREFIX/bin/x86_64-apple-darwin13.4.0-clang  
+ ./bootstrap.sh --prefix=$PREFIX --with-toolset=clang --with-icu=$PREFIX --with-python=$PREFIX/bin/python '--with-python-root=$PREFIX : $PREFIX/include/python3.9'  
Building B2 engine..  
  
###  
###  
### Using 'clang' toolset.  
###  
###  
  
clang version 18.1.8  
Target: x86_64-apple-darwin13.4.0  
Thread model: posix  
InstalledDir: $BUILD_PREFIX/bin  
  
###  
###  
  
> $BUILD_PREFIX/bin/x86_64-apple-darwin13.4.0-clang++ -x c++ -std=c++11 -march=core2 -mssse3 -ftree-vectorize -fPIC -fstack-protector-strong -O2 -pipe -stdlib=libc++ -fvisibility-inlines-hidden -fmessage-length=0 -isystem $PREFIX/include -fdebug-prefix-map=$SRC_DIR=/usr/local/src/conda/boost-split-1.89.0 -fdebug-prefix-map=$PREFIX=/usr/local/src/conda-prefix -fPIC -D_LIBCPP_DISABLE_AVAILABILITY -O3 -s -Wno-deprecated-declarations -DNDEBUG bindjam.cpp builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp events.cpp execcmd.cpp execnt.cpp execunix.cpp filent.cpp filesys.cpp fileunix.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam_strings.cpp jam.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp pathunix.cpp regexp.cpp rules.cpp scan.cpp search.cpp startup.cpp tasks.cpp timestamp.cpp value.cpp variable.cpp w32_getreg.cpp mod_args.cpp mod_command_db.cpp mod_db.cpp mod_jam_builtin.cpp mod_jam_class.cpp mod_jam_errors.cpp mod_jam_modules.cpp mod_order.cpp mod_path.cpp mod_property_set.cpp mod_regex.cpp mod_sequence.cpp mod_set.cpp mod_string.cpp mod_summary.cpp mod_sysinfo.cpp mod_version.cpp -o b2  
ld: warning: option -s is obsolete and being ignored  
Undefined symbols for architecture x86_64:  
  "std::__1::basic_stringbuf<char, std::__1::char_traits<char>, std::__1::allocator<char>>::str() const", referenced from:  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, int>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, int&) in jam-6cb64e.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, long>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, long&) in jam-6cb64e.o  
      b2::args::process_args(bool) in mod_args-7a3d71.o  
  "VTT for std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      bool lyra::detail::to_string<int>(int const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>&) in jam-6cb64e.o  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_stringstream[abi:ne180100]() in jam-6cb64e.o  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::~basic_stringstream() in jam-6cb64e.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, int>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, int&) in jam-6cb64e.o  
      bool lyra::detail::to_string<long>(long const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>&) in jam-6cb64e.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, long>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, long&) in jam-6cb64e.o  
  "VTT for std::__1::basic_ostringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      b2::args::process_args(bool) in mod_args-7a3d71.o  
      std::__1::basic_ostringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::~basic_ostringstream() in mod_args-7a3d71.o  
  "vtable for std::__1::basic_stringbuf<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      bool lyra::detail::to_string<int>(int const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>&) in jam-6cb64e.o  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_stringstream[abi:ne180100]() in jam-6cb64e.o  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::~basic_stringstream() in jam-6cb64e.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, int>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, int&) in jam-6cb64e.o  
      bool lyra::detail::to_string<long>(long const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>&) in jam-6cb64e.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, long>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, long&) in jam-6cb64e.o  
      b2::args::process_args(bool) in mod_args-7a3d71.o  
      ...  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
  "vtable for std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_stringstream[abi:ne180100]() in jam-6cb64e.o  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
  "vtable for std::__1::basic_ostringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      b2::args::process_args(bool) in mod_args-7a3d71.o  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
ld: symbol(s) not found for architecture x86_64  
x86_64-apple-darwin13.4: error: linker command failed with exit code 1 (use -v to see invocation)  
  
Failed to build B2 build engine  
```  
  
</details>  
  
  
<details>  
<summary>boost 1.89 & clang 19</summary>  
  
```  
+ CFLAGS='-march=core2 -mssse3 -ftree-vectorize -fPIC -fstack-protector-strong -O2 -pipe -isystem $PREFIX/include -fdebug-prefix-map=/Users/runner/miniforge3/conda-bld/boost-split_1755208185612/work=/usr/local/src/conda/boost-split-1.89.0 -fdebug-prefix-map=$PREFIX=/usr/local/src/conda-prefix'  
+ CXX=$BUILD_PREFIX/bin/x86_64-apple-darwin13.4.0-clang++  
+ CC=$BUILD_PREFIX/bin/x86_64-apple-darwin13.4.0-clang  
+ ./bootstrap.sh --prefix=$PREFIX --with-toolset=clang --with-icu=$PREFIX --with-python=$PREFIX/bin/python '--with-python-root=$PREFIX : $PREFIX/include/python3.13'  
Building B2 engine..  
  
###  
###  
### Using 'clang' toolset.  
###  
###  
  
clang version 19.1.7  
Target: x86_64-apple-darwin13.4.0  
Thread model: posix  
InstalledDir: $BUILD_PREFIX/bin  
  
###  
###  
  
> $BUILD_PREFIX/bin/x86_64-apple-darwin13.4.0-clang++ -x c++ -std=c++11 -march=core2 -mssse3 -ftree-vectorize -fPIC -fstack-protector-strong -O2 -pipe -stdlib=libc++ -fvisibility-inlines-hidden -fmessage-length=0 -isystem $PREFIX/include -fdebug-prefix-map=$SRC_DIR=/usr/local/src/conda/boost-split-1.89.0 -fdebug-prefix-map=$PREFIX=/usr/local/src/conda-prefix -fPIC -D_LIBCPP_DISABLE_AVAILABILITY -O3 -s -Wno-deprecated-declarations -DNDEBUG bindjam.cpp builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp events.cpp execcmd.cpp execnt.cpp execunix.cpp filent.cpp filesys.cpp fileunix.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam_strings.cpp jam.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp pathunix.cpp regexp.cpp rules.cpp scan.cpp search.cpp startup.cpp tasks.cpp timestamp.cpp value.cpp variable.cpp w32_getreg.cpp mod_args.cpp mod_command_db.cpp mod_db.cpp mod_jam_builtin.cpp mod_jam_class.cpp mod_jam_errors.cpp mod_jam_modules.cpp mod_order.cpp mod_path.cpp mod_property_set.cpp mod_regex.cpp mod_sequence.cpp mod_set.cpp mod_string.cpp mod_summary.cpp mod_sysinfo.cpp mod_version.cpp -o b2  
ld: warning: option -s is obsolete and being ignored  
Undefined symbols for architecture x86_64:  
  "std::__1::basic_stringbuf<char, std::__1::char_traits<char>, std::__1::allocator<char>>::str() const", referenced from:  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, int>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, int&) in jam-c1ca95.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, long>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, long&) in jam-c1ca95.o  
      b2::args::process_args(bool) in mod_args-477dae.o  
  "std::__1::bad_function_call::~bad_function_call()", referenced from:  
      std::__1::__throw_bad_function_call[abi:ne190107]() in events-d08cba.o  
      std::__1::__throw_bad_function_call[abi:ne190107]() in function-eda9b1.o  
      std::__1::__throw_bad_function_call[abi:ne190107]() in tasks-e09e2e.o  
++ echo -march=core2 -mtune=haswell -mssse3 -ftree-vectorize -fPIC -fstack-protector-strong -O2 -pipe -isystem $PREFIX/include -fdebug-prefix-map=/Users/runner/miniforge3/conda-bld/boost-split_1755208185612/work=/usr/local/src/conda/boost-split-1.89.0 -fdebug-prefix-map=$PREFIX=/usr/local/src/conda-prefix  
++ sed 's/ -mcpu=[^ ]*//g'  
++ sed 's/ -mtune=[^ ]*//g'  
+ CFLAGS='-march=core2 -mssse3 -ftree-vectorize -fPIC -fstack-protector-strong -O2 -pipe -isystem $PREFIX/include -fdebug-prefix-map=/Users/runner/miniforge3/conda-bld/boost-split_1755208185612/work=/usr/local/src/conda/boost-split-1.89.0 -fdebug-prefix-map=$PREFIX=/usr/local/src/conda-prefix'  
+ CXX=$BUILD_PREFIX/bin/x86_64-apple-darwin13.4.0-clang++  
+ CC=$BUILD_PREFIX/bin/x86_64-apple-darwin13.4.0-clang  
+ ./bootstrap.sh --prefix=$PREFIX --with-toolset=clang --with-icu=$PREFIX --with-python=$PREFIX/bin/python '--with-python-root=$PREFIX : $PREFIX/include/python3.13'  
      std::__1::__throw_bad_function_call[abi:ne190107]() in tasks-e09e2e.o  
  "typeinfo for std::__1::bad_function_call", referenced from:  
      std::__1::__throw_bad_function_call[abi:ne190107]() in events-d08cba.o  
      std::__1::__throw_bad_function_call[abi:ne190107]() in function-eda9b1.o  
      std::__1::__throw_bad_function_call[abi:ne190107]() in tasks-e09e2e.o  
  "VTT for std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      bool lyra::detail::to_string<int>(int const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>&) in jam-c1ca95.o  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_stringstream[abi:ne190107]() in jam-c1ca95.o  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::~basic_stringstream() in jam-c1ca95.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, int>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, int&) in jam-c1ca95.o  
      bool lyra::detail::to_string<long>(long const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>&) in jam-c1ca95.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, long>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, long&) in jam-c1ca95.o  
  "VTT for std::__1::basic_ostringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      b2::args::process_args(bool) in mod_args-477dae.o  
      std::__1::basic_ostringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::~basic_ostringstream() in mod_args-477dae.o  
  "vtable for std::__1::basic_stringbuf<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      bool lyra::detail::to_string<int>(int const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>&) in jam-c1ca95.o  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_stringstream[abi:ne190107]() in jam-c1ca95.o  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::~basic_stringstream() in jam-c1ca95.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, int>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, int&) in jam-c1ca95.o  
      bool lyra::detail::to_string<long>(long const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>&) in jam-c1ca95.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, long>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, long&) in jam-c1ca95.o  
      b2::args::process_args(bool) in mod_args-477dae.o  
      ...  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
  "vtable for std::__1::bad_function_call", referenced from:  
      std::__1::__throw_bad_function_call[abi:ne190107]() in events-d08cba.o  
      std::__1::__throw_bad_function_call[abi:ne190107]() in function-eda9b1.o  
      std::__1::__throw_bad_function_call[abi:ne190107]() in tasks-e09e2e.o  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
  "vtable for std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_stringstream[abi:ne190107]() in jam-c1ca95.o  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
  "vtable for std::__1::basic_ostringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      b2::args::process_args(bool) in mod_args-477dae.o  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
ld: symbol(s) not found for architecture x86_64  
x86_64-apple-darwin13.4: error: linker command failed with exit code 1 (use -v to see invocation)  
  
Failed to build B2 build engine  
```  
  
</details>  
  
  
<details>  
<summary>boost 1.89 & clang 20</summary>  
  
```  
+ CFLAGS='-march=core2 -mssse3 -ftree-vectorize -fPIC -fstack-protector-strong -O2 -pipe -isystem $PREFIX/include -fdebug-prefix-map=/Users/runner/miniforge3/conda-bld/boost-split_1755317869811/work=/usr/local/src/conda/boost-split-1.89.0 -fdebug-prefix-map=$PREFIX=/usr/local/src/conda-prefix'  
+ CXX=$BUILD_PREFIX/bin/x86_64-apple-darwin13.4.0-clang++  
+ CC=$BUILD_PREFIX/bin/x86_64-apple-darwin13.4.0-clang  
+ ./bootstrap.sh --prefix=$PREFIX --with-toolset=clang --with-icu=$PREFIX --with-python=$PREFIX/bin/python '--with-python-root=$PREFIX : $PREFIX/include/python3.10'  
Building B2 engine..  
  
###  
###  
### Using 'clang' toolset.  
###  
###  
  
clang version 20.1.8  
Target: x86_64-apple-darwin13.4.0  
Thread model: posix  
InstalledDir: $BUILD_PREFIX/bin  
  
###  
###  
  
> $BUILD_PREFIX/bin/x86_64-apple-darwin13.4.0-clang++ -x c++ -std=c++11 -march=core2 -mssse3 -ftree-vectorize -fPIC -fstack-protector-strong -O2 -pipe -stdlib=libc++ -fvisibility-inlines-hidden -fmessage-length=0 -isystem $PREFIX/include -fdebug-prefix-map=$SRC_DIR=/usr/local/src/conda/boost-split-1.89.0 -fdebug-prefix-map=$PREFIX=/usr/local/src/conda-prefix -fPIC -D_LIBCPP_DISABLE_AVAILABILITY -O3 -s -Wno-deprecated-declarations -DNDEBUG bindjam.cpp builtins.cpp class.cpp command.cpp compile.cpp constants.cpp cwd.cpp debug.cpp debugger.cpp events.cpp execcmd.cpp execnt.cpp execunix.cpp filent.cpp filesys.cpp fileunix.cpp frames.cpp function.cpp glob.cpp hash.cpp hcache.cpp hdrmacro.cpp headers.cpp jam_strings.cpp jam.cpp jamgram.cpp lists.cpp make.cpp make1.cpp md5.cpp mem.cpp modules.cpp native.cpp output.cpp parse.cpp pathnt.cpp pathsys.cpp pathunix.cpp regexp.cpp rules.cpp scan.cpp search.cpp startup.cpp tasks.cpp timestamp.cpp value.cpp variable.cpp w32_getreg.cpp mod_args.cpp mod_command_db.cpp mod_db.cpp mod_jam_builtin.cpp mod_jam_class.cpp mod_jam_errors.cpp mod_jam_modules.cpp mod_order.cpp mod_path.cpp mod_property_set.cpp mod_regex.cpp mod_sequence.cpp mod_set.cpp mod_string.cpp mod_summary.cpp mod_sysinfo.cpp mod_version.cpp -o b2  
ld: warning: option -s is obsolete and being ignored  
Undefined symbols for architecture x86_64:  
  "std::__1::basic_stringbuf<char, std::__1::char_traits<char>, std::__1::allocator<char>>::str() const", referenced from:  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, int>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, int&) in jam-24f7ba.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, long>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, long&) in jam-24f7ba.o  
      b2::args::process_args(bool) in mod_args-8870e0.o  
  "std::__1::bad_function_call::~bad_function_call()", referenced from:  
      std::__1::__throw_bad_function_call[abi:ne200100]() in events-470f33.o  
      std::__1::__throw_bad_function_call[abi:ne200100]() in function-111435.o  
      std::__1::__throw_bad_function_call[abi:ne200100]() in tasks-83e949.o  
  "typeinfo for std::__1::bad_function_call", referenced from:  
      std::__1::__throw_bad_function_call[abi:ne200100]() in events-470f33.o  
      std::__1::__throw_bad_function_call[abi:ne200100]() in function-111435.o  
      std::__1::__throw_bad_function_call[abi:ne200100]() in tasks-83e949.o  
  "VTT for std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      bool lyra::detail::to_string<int>(int const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>&) in jam-24f7ba.o  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_stringstream[abi:ne200100]() in jam-24f7ba.o  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::~basic_stringstream() in jam-24f7ba.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, int>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, int&) in jam-24f7ba.o  
      bool lyra::detail::to_string<long>(long const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>&) in jam-24f7ba.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, long>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, long&) in jam-24f7ba.o  
  "VTT for std::__1::basic_ostringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      std::__1::basic_ostringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_ostringstream[abi:ne200100]() in mod_args-8870e0.o  
      std::__1::basic_ostringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::~basic_ostringstream() in mod_args-8870e0.o  
  "vtable for std::__1::basic_stringbuf<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      bool lyra::detail::to_string<int>(int const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>&) in jam-24f7ba.o  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_stringstream[abi:ne200100]() in jam-24f7ba.o  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::~basic_stringstream() in jam-24f7ba.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, int>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, int&) in jam-24f7ba.o  
      bool lyra::detail::to_string<long>(long const&, std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>&) in jam-24f7ba.o  
      bool lyra::detail::from_string<std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>>, long>(std::__1::basic_string<char, std::__1::char_traits<char>, std::__1::allocator<char>> const&, long&) in jam-24f7ba.o  
      std::__1::basic_ostringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_ostringstream[abi:ne200100]() in mod_args-8870e0.o  
      ...  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
  "vtable for std::__1::bad_function_call", referenced from:  
      std::__1::__throw_bad_function_call[abi:ne200100]() in events-470f33.o  
      std::__1::__throw_bad_function_call[abi:ne200100]() in function-111435.o  
      std::__1::__throw_bad_function_call[abi:ne200100]() in tasks-83e949.o  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
  "vtable for std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      std::__1::basic_stringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_stringstream[abi:ne200100]() in jam-24f7ba.o  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
  "vtable for std::__1::basic_ostringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>", referenced from:  
      std::__1::basic_ostringstream<char, std::__1::char_traits<char>, std::__1::allocator<char>>::basic_ostringstream[abi:ne200100]() in mod_args-8870e0.o  
  NOTE: a missing vtable usually means the first non-inline virtual member function has no definition.  
ld: symbol(s) not found for architecture x86_64  
x86_64-apple-darwin13.4: error: linker command failed with exit code 1 (use -v to see invocation)  
  
Failed to build B2 build engine  
```  
  
</details>

---

## Comment 4

> Username: sdarwin  
> Created at: 2025-08-19 21:19:37 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3202273084  

Questions:  
  
1. With "Clang 20", is that on macOS?  Installed via `XCode`, `brew`, or manually compiled, or which method?    
  
2. In terms of MacOS in particular, let's focus on the officially support XCode versions of clang.  Which of those have you tested, and what are the exact version combinations:  clang/xcode.  
   
3. Have you tried clang 18, 19, 20 on Linux?

---

## Comment 5

> Username: h-vetinari  
> Created at: 2025-08-19 22:42:30 UTC  
> Updated at: 2025-08-19 22:45:16 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3202547958  

All clang's I'm talking about are from https://github.com/llvm/llvm-project, which is the open-source upstream of what eventually ends up (with a different version scheme) in "Apple Clang". It is arguably the more important clang flavour (e.g. [cppreference](https://en.cppreference.com/w/cpp/compiler_support.html) orders compilers as GCC, Clang, MSVC, Apple Clang, the rest).  
  
> In terms of MacOS in particular, let's focus on the officially support XCode versions of clang.  
  
Xcode and Apple Clang are not freely distributable due to licensing reasons, which is why many people (our ecosystem included) are using the "vanilla clang", and have been for years and years.  
  
Also, the clang versions being referenced in the boost docs and release notes are for "vanilla clang", not "Apple Clang" or Xcode. So this is not asking for a change of priorities, just the testing of current versions for an unchanged set of toolchains.  
  
> Have you tried clang 18, 19, 20 on Linux?  
  
I can if you'd like. None of these problems look particularly terrifying, it just needs to be tested regularly.

---

## Comment 6

> Username: sdarwin  
> Created at: 2025-08-19 23:26:10 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3202885357  

"it just needs to be tested regularly".      
That sounds very commonsensical.  I agree.  At the same time, consider boostorg/unordered, for example.  It's already running 60 tests on every commit made to the repository. That's a lot. And then you are saying "Well, it should be 65 or 70 instead of 60, because you need to add in some "vanilla clang macos" testing also, and be sure to update that every few months so it points to the new version of vanilla clang on macos.   Ideally, yes.

---

## Comment 7

> Username: h-vetinari  
> Created at: 2025-08-20 00:06:48 UTC  
> Updated at: 2025-08-20 01:24:25 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3203265297  

> And then you are saying "Well, it should be 65 or 70 instead of 60, because you need to add in some "vanilla clang macos" testing also  
  
You already have vanilla clang testing. :)  
  
And adding new versions doesn't _have to_ mean increasing the number of tested combinations, you could simply drop off some of the old ones. As I noted in the OP:  
  
> Note, I'm not advocating to test the _entire_ version range. For example, I hardly think that testing Clang 3.5, 3.6, 3.7, 3.8, 3.9 (in addition to 3.4) has any benefits in 2025. I leave the question of whether resp. how to thin out the list to the maintainers here, but the newer end should be supported.  
  
As another example, C++03 testing could be _drastically_ thinned out. That language mode exists purely for compatibility, and it's way more important to test on C++23/26 (because users who can't use new boost on C++03 still have all the old releases, whereas boost users needing C++23 have no alternative if something is broken).  
  
For argument's sake, for C++20 you could do  
```diff  
-Clang, C++20: 11.0.0, 12.0.0, 13.0.0, 14.0.0, 15.0.0  
+Clang, C++20: 15.0.0, 17.1.0, 18.1.0, 19.1.0, 20.1.0  
```  
  
Combining the most recent boost with ancient compilers like clang 3.x is an extremely niche use case, whereas using recent boost with recent compilers is always going to be a predominant pattern.

---

## Comment 8

> Username: sdarwin  
> Created at: 2025-08-20 14:30:14 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3206667705  

@h-vetinari  Boost is open source, maintained by volunteers, and also the community. You are reporting a bug in "boost 1.89 & clang 20".  If you are able to debug the issue, send a pull request to the applicable boost library where that bug exists. If you discover the affected library wasn't testing with clang 20, send them a pull request to modify the CI file.   Then you could  comment back in this github issue, showing the CI update example.

---

## Comment 9

> Username: MarDiehl  
> Created at: 2025-08-20 20:26:56 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3207990443  

@sdarwin: I reported a similar issue related to the Intel compiler suite (oneAPI) which is frequently used in high performance computing: https://github.com/bfgroup/b2/issues/413 and https://github.com/boostorg/boost/issues/952 and would be interested in contributing tests. I'm fairly familiar with GitHub actions, but I don't understand the scheduled pipelines setup and CircleCI. Do you have any instructions or could provide guidance?  
  
I also want to remark that  find it unfortunate that the set of setups used for testing differs a lot between the individual subprojects. Wouldn't it make sense to coordinate the supported systems or even provide a template or GitHub action that can/should be used by all libraries and is updated regularly by a central instance? It seems that testing with Intel requires currently a MR for each individual subproject.  
Sorry if this is a naive suggestion and the individual maintainers insist on their freedom, but it requires a lot of time to coordinate a change in 160+ repositories.

---

## Comment 10

> Username: sdarwin  
> Created at: 2025-08-20 20:34:47 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3208010570  

There is an intention to use a centralized repeatable configuration.    
https://github.com/boostorg/boost-ci/tree/master/.github/workflows/   
Each boost library would include `ci.yml`.  That calls `reusable.yml`, which can be updated once from boost-ci itself.     It's "in production" already. Although not everywhere by any means.    
If you send a MR to boost-ci, it would begin to roll out to a few boost libraries, to start with.

---

## Comment 11

> Username: h-vetinari  
> Created at: 2025-08-20 22:06:10 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3208217591  

> There is an intention to use a centralized repeatable configuration.  
  
That looks great actually! If this were used by all subprojects, you'd already have coverage of newer [GCC](https://github.com/boostorg/boost-ci/blob/e8241d7f0c9ccd9f04d2326268008c7bd29eeef1/.github/workflows/reusable.yml#L133-L139) and [clang](https://github.com/boostorg/boost-ci/blob/e8241d7f0c9ccd9f04d2326268008c7bd29eeef1/.github/workflows/reusable.yml#L158-L161), at least on linux.  
  
In contrast, the macOS [jobs](https://github.com/boostorg/boost-ci/blob/e8241d7f0c9ccd9f04d2326268008c7bd29eeef1/.github/workflows/reusable.yml#L169-L171) are pretty thin though, which perhaps also explains the conditions that lead to the regression I observed.  
  
> Boost is open source, maintained by volunteers, and also the community. You are reporting a bug in "boost 1.89 & clang 20". If you are able to debug the issue, send a pull request to the applicable boost library where that bug exists.  
  
Sidenote: I'm not a user of boost, but a distributor (with 100s of projects building atop boost in our ecosystem, and 100'000s of users consuming boost through one of those builds). I have to split my time between 100s of packages I take care of; as such I'm interested in long-term solutions for avoiding regressions (and I could try to help set up testing of "vanilla clang" on macOS), but I won't be able to chase down individual issues.

---

## Comment 12

> Username: MarDiehl  
> Created at: 2025-08-21 04:52:38 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3208990988  

This indeed looks great, my attempt to include icpx is here: https://github.com/boostorg/boost-ci/pull/299

---

## Comment 13

> Username: Flamefire  
> Created at: 2025-08-26 11:34:48 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3223795731  

> building boost 1.88 using the b2-bootstrap is broken with clang 19 on osx (clang 18 works), while [boost 1.89](https://github.com/conda-forge/boost-feedstock/pull/236) is broken[1](#user-content-fn-1-e935695ede3b63ed008aa744ef4a24b9) with all of: clang 18, 19 and 20.  
>   
> Going all the way back to clang 17 (almost 2 years old by now) "fixes" things, but this is a bit ridiculous TBH. I understand how difficult open source maintenance is (check my GH profile), but in an age of godbolt and various other ways to easily get pre-built and up-to-date toolchains, there really isn't a reason for not testing with up-to-date compilers.  
>   
  
Looking at the error logs you included it shows various issues related to the standard library, not Boost in particular.  
  
Those issues are seen in other projects with brew-installed clang where it ends up using new libc++ headers but links to the system libc++ library. So it looks like a misconfiguration issue.  
  
Can you confirm that this is or is not the case?

---

## Comment 14

> Username: sdarwin  
> Created at: 2025-08-26 12:18:16 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3223943259  

did you post the issue in this thread:    
https://github.com/Homebrew/homebrew-core/issues/169820

---

## Comment 15

> Username: h-vetinari  
> Created at: 2025-08-30 08:55:33 UTC  
> Updated at: 2025-08-30 09:00:38 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3239117463  

> Those issues are seen in other projects with brew-installed clang where it ends up using new libc++ headers but links to the system libc++ library. So it looks like a misconfiguration issue.  
  
Actually, the issue is a different one. Conda-forge supports older macOSX deployment targets (currently defaulting to 10.13), which we can get away with because we are always able to ship our own libc++ in user environments, and not dependent on what's available in the system. We [patch](https://github.com/conda-forge/libcxx-feedstock/blob/ce46a65810ba4f78459a2a631dacf95fa8df778f/recipe/patches/0002-custom-error-message-for-old-sdk.patch) libc++ to point to our [docs](https://conda-forge.org/docs/maintainer/knowledge_base.html#newer-c-features-with-old-sdk) which boil down to setting `CXXFLAGS="${CXXFLAGS} -D_LIBCPP_DISABLE_AVAILABILITY"`.  
  
We're already doing that in the case of boost, but what seems to have happened is that the b2 boostrap build does not respect `CXXFLAGS` anymore(?), and/or does something unusual in looking up the SDK[^1]  
  
[^1]: though I admit that I don't fully understand why clang 17 works and clang 19 doesn't... Probably clang 19 uses newer libc++ functionality by default, perhaps too new for old macos.  
  
The one we provide is not the system one (well, the images still contain it, and the one in the macos-13 images would be new enough), rather we provide a configurable choice that defaults to the deployment target unless overridden (higher SDK doesn't automatically imply higher deployment target, but that is the case when it's the other way around).  
  
> Can you confirm that this is or is not the case?  
  
I tested this in https://github.com/conda-forge/boost-feedstock/pull/240; using both SDK (and deployment_target) 13.3 resp. 12.3 worked fine, 11.3 is not sufficient though. I can work around this by just providing a newer `MACOSX_SDK_VERSION`, but ideally that would not be necessary.  
  
Zooming out a bit, it would be good for boost to decide and communicate a strategy for what macOS versions are supported, whether that's some fixed number of years after release, or "the oldest version provided by GHA", or whatever. Lots of projects are having these discussions, e.g. [conda-forge](https://github.com/conda-forge/conda-forge.github.io/issues/2467), [python](https://discuss.python.org/t/macos-version-support-policy/53715), libc++, chrome, etc. It would be good to have some policy to point to, then it'd also be easy to decide if we simply raise the deployment target for boost-dependent projects, for example.

---

## Comment 16

> Username: Flamefire  
> Created at: 2025-08-30 11:29:23 UTC  
> Url: https://github.com/boostorg/boost/issues/990#issuecomment-3239203634  

> it would be good for boost to decide and communicate a strategy for what macOS versions are supported, whether that's some fixed number of years after release, or "the oldest version provided by GHA", or whatever.  
  
I agree that a more consistent testing strategy would help, however Boost library authors are free to test how/what they find suitable.     
That being said there are efforts to have a common CI config which is based on GHA. That recently added clang-18 on MacOS-15:  
https://github.com/boostorg/boost-ci/blob/7e75b6e0d8bc281a8a8e8bb7ae6e771c14fc937a/.github/workflows/reusable.yml#L171-L175  
  
So "the oldest version provided by GHA" is actually a good approximation: There currently isn't a good alternative to GHA for testing macOS due to their OS/legal constraints. Hence what isn't on GHA anymore cannot be easily tested and might no longer be supported even though it likely will continue to work fine for quite some time as it did before and other still-tested CI configurations have some good overlap with the macOS environment (e.g. various clang w/ libc++ tests) to notice potential breakages outside of macOS testing
