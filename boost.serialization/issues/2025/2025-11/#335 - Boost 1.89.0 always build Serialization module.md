# #335 - [bug] Boost 1.89.0 always build Serialization module [Open]

> Username: uilianries  
> Created at: 2025-11-03 11:40:08 UTC  
> Updated at: 2025-11-04 18:40:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/335  

Greetings!  
  
This bug was originally reported by @stevenwdv on the issue https://github.com/conan-io/conan-center-index/issues/28801.  
  
When building Boost 1.89.0 from source, Boost Serialization is always built, even when configured not to be built. The same is not true for Boost 1.88.0, it works as expected: It does not generate boost_serialization when disabled;   
  
BTW, I see no changes from 1.88.0 to 1.89.0: https://github.com/boostorg/serialization/compare/boost-1.88.0...boost-1.89.0 So it seems related to a transitive module.   
  
### Environment  
  
OS: Linux (Ubuntu 22.04)  
Compiler: GCC 11  
Arch: AMD64  
Build Type: Release  
B2 Options: `--without-serialization --without-graph --without-wave  link=static`  
  
### Steps to Reproduce  
  
```  
wget https://archives.boost.io/release/1.89.0/source/boost_1_89_0.tar.bz2  
tar jxf boost_1_89_0.tar.bz2  
cd boost_1_89_0/  
./bootstrap.sh  
./b2 --without-serialization --without-graph --without-wave  link=static install --prefix=/tmp/install-boost  
find /tmp/install-boost/lib -type f -name "*serialization*"  
/tmp/install-boost/lib/libboost_serialization.a  
```  
  
### Build Log  
  
Full build log shows disabled serialization, but installs `libboost_serialization.a`.  
  
[boost-1.89.0-no-serialization.log](https://github.com/user-attachments/files/23301452/boost-1.89.0-no-serialization.log)  
  
Regards.

---

## Comment 1

> Username: pdimov  
> Created at: 2025-11-03 16:19:01 UTC  
> Url: https://github.com/boostorg/serialization/issues/335#issuecomment-3481371773  

The issue is probably a transitive dependency  
  
something compiled that is being built -> something header-only -> serialization  
  
that wasn't visible to b2 before, because header-only libraries didn't have Jamfiles, but is now, because they do.

---

## Comment 2

> Username: pdimov  
> Created at: 2025-11-04 10:44:52 UTC  
> Url: https://github.com/boostorg/serialization/issues/335#issuecomment-3485261675  

Or it could be because Python depends on Graph? If you use --without-python, does it still build Serialization?

---

## Comment 3

> Username: pdimov  
> Created at: 2025-11-04 10:47:48 UTC  
> Url: https://github.com/boostorg/serialization/issues/335#issuecomment-3485277451  

Yes, I see libboost_graph.a being built in the log.

---

## Comment 4

> Username: uilianries  
> Created at: 2025-11-04 11:36:56 UTC  
> Url: https://github.com/boostorg/serialization/issues/335#issuecomment-3485540193  

@pdimov Hello!   
  
> Or it could be because Python depends on Graph? If you use --without-python, does it still build Serialization?  
  
When using `--without-python`, the result is still the same: serialization library is built/installed.  
  
Here is my build log using `--without-python`  
  
[boost-1.88.0-no-deps.log](https://github.com/user-attachments/files/23331023/boost-1.88.0-no-deps.log)

---

## Comment 5

> Username: pdimov  
> Created at: 2025-11-04 11:57:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/335#issuecomment-3485623321  

My next guess, then, is that Log depends on PropertyTree, which depends on Serialization.  
  
https://pdimov.github.io/boostdep-report/boost-1.89.0/log.html#property_tree  
https://pdimov.github.io/boostdep-report/boost-1.89.0/property_tree.html#serialization  
  
Why is it important for you that Serialization not be built?

---

## Comment 6

> Username: uilianries  
> Created at: 2025-11-04 11:58:54 UTC  
> Url: https://github.com/boostorg/serialization/issues/335#issuecomment-3485627954  

@pdimov Doing a grep to find `serialization` on Boost 1.89.0 build files, I have the following result:  
  
```  
find . -type f -name "*jam*" -o -name "Jamfile.v2"  -exec grep 'serialization' {} +  
./libs/flyweight/example/Jamfile.v2:exe serialization  
./libs/flyweight/example/Jamfile.v2:    : serialization.cpp  
./libs/flyweight/example/Jamfile.v2:      /boost/serialization//boost_serialization/<link>static  
./libs/flyweight/test/Jamfile.v2:    [ run test_serialization.cpp      test_serialization_main.cpp  
./libs/flyweight/test/Jamfile.v2:          /boost/serialization//boost_serialization/<link>static       ]  
./libs/multi_index/example/Jamfile.v2:exe serialization  
./libs/multi_index/example/Jamfile.v2:    : serialization.cpp  
./libs/multi_index/example/Jamfile.v2:      /boost/serialization//boost_serialization  
./libs/multi_index/test/Jamfile.v2:    [ run test_serialization.cpp    test_serialization1.cpp  
./libs/multi_index/test/Jamfile.v2:          test_serialization2.cpp   test_serialization3.cpp  
./libs/multi_index/test/Jamfile.v2:          test_serialization_main.cpp  
./libs/multi_index/test/Jamfile.v2:          /boost/serialization//boost_serialization                ]  
./libs/dynamic_bitset/test/Jamfile.v2:# due to https://github.com/boostorg/serialization/issues/108  
./libs/dynamic_bitset/test/Jamfile.v2:  test-suite dynamic_bitset_serialization :  
./libs/dynamic_bitset/test/Jamfile.v2:             <library>/boost/serialization//boost_serialization  
./libs/multiprecision/test/Jamfile.v2:      [ run test_cpp_int_serial.cpp /boost/serialization//boost_serialization : : : release <define>TEST1 <toolset>gcc-mingw:<link>static : test_cpp_int_serial_1 ]  
./libs/multiprecision/test/Jamfile.v2:      [ run test_cpp_int_serial.cpp /boost/serialization//boost_serialization : : : release <define>TEST2 <toolset>gcc-mingw:<link>static : test_cpp_int_serial_2 ]  
./libs/multiprecision/test/Jamfile.v2:      [ run test_cpp_int_serial.cpp /boost/serialization//boost_serialization : : : release <define>TEST3 <toolset>gcc-mingw:<link>static : test_cpp_int_serial_3 ]  
./libs/multiprecision/test/Jamfile.v2:      [ run test_cpp_int_serial.cpp /boost/serialization//boost_serialization : : : release <define>TEST4 <toolset>gcc-mingw:<link>static : test_cpp_int_serial_4 ]  
./libs/multiprecision/test/Jamfile.v2:      [ run test_cpp_int_deserial.cpp /boost/serialization//boost_serialization /boost/filesystem//boost_filesystem : $(here)/serial_txts : : release <toolset>gcc-mingw:<link>static  ]  
./libs/multiprecision/test/Jamfile.v2:      [ run test_cpp_rat_serial.cpp /boost/serialization//boost_serialization : : : release <toolset>gcc-mingw:<link>static  ]  
./libs/multiprecision/test/Jamfile.v2:      [ run test_adapt_serial.cpp /boost/serialization//boost_serialization : : : release <toolset>gcc-mingw:<link>static  ]  
./libs/multiprecision/test/Jamfile.v2:      [ run test_cpp_dec_float_serial.cpp /boost/serialization//boost_serialization : : : release <define>TEST1 <toolset>gcc-mingw:<link>static  : test_cpp_dec_float_serial_1 ]  
./libs/multiprecision/test/Jamfile.v2:      [ run test_cpp_dec_float_serial.cpp /boost/serialization//boost_serialization : : : release <define>TEST2 <toolset>gcc-mingw:<link>static  : test_cpp_dec_float_serial_2 ]  
./libs/multiprecision/test/Jamfile.v2:      [ run test_float128_serial.cpp /boost/serialization//boost_serialization quadmath : : : release <toolset>gcc-mingw:<link>static  [ check-target-builds ../config//has_float128 : : <build>no ]  ]  
./libs/multiprecision/test/Jamfile.v2:      [ run test_cpp_bin_float_serial.cpp /boost/serialization//boost_serialization : : : release <toolset>gcc-mingw:<link>static  <define>TEST1 : test_bin_dec_float_serial_1 ]  
./libs/multiprecision/test/Jamfile.v2:      [ run test_cpp_bin_float_serial.cpp /boost/serialization//boost_serialization : : : release <define>TEST2 <toolset>gcc-mingw:<link>static  : test_bin_dec_float_serial_2 ]  
./libs/date_time/test/Jamfile.v2:     ../../serialization/build//boost_serialization  
./libs/date_time/test/Jamfile.v2:    ../../serialization/build//boost_serialization  
./libs/date_time/test/Jamfile.v2:    ../../serialization/build//boost_serialization  
./libs/date_time/test/Jamfile.v2:    ../../serialization/build//boost_serialization  
./libs/date_time/test/Jamfile.v2:    ../../serialization/build//boost_serialization  
./libs/date_time/test/Jamfile.v2:    ../../serialization/build//boost_serialization  
./libs/date_time/test/Jamfile.v2:    ../../serialization/build//boost_serialization  
./libs/date_time/test/Jamfile.v2:    ../../serialization/build//boost_serialization  
./libs/wave/tool/build/Jamfile.v2:    /boost/serialization//boost_serialization  
./libs/ptr_container/test/Jamfile.v2:run serialization.cpp /boost/serialization//boost_serialization : : : <undefined-sanitizer>norecover:<build>no ;  
./libs/unordered/test/Jamfile.v2:run unordered/serialization_tests.cpp  
./libs/unordered/test/Jamfile.v2:      <library>/boost/serialization//boost_serialization/<warnings>off  
./libs/unordered/test/Jamfile.v2:  serialization_tests  
./libs/unordered/test/Jamfile.v2:run unordered/serialization_tests.cpp  
./libs/unordered/test/Jamfile.v2:      <library>/boost/serialization//boost_serialization/<warnings>off  
./libs/unordered/test/Jamfile.v2:    : foa_serialization_tests ;  
./libs/unordered/test/Jamfile.v2:  foa_serialization_tests  
./libs/unordered/test/Jamfile.v2:run cfoa/serialization_tests.cpp  
./libs/unordered/test/Jamfile.v2:      <library>/boost/serialization//boost_serialization/<warnings>off  
./libs/unordered/test/Jamfile.v2:    : cfoa_serialization_tests ;  
./libs/unordered/test/Jamfile.v2:  cfoa_serialization_tests  
./libs/pool/test/Jamfile.v2:    [ run pool_msvc_compiler_bug_test.cpp : : : <library>/boost/serialization//boost_serialization <toolset>msvc:<cxxflags>-wd4512 ]  
./libs/uuid/test/Jamfile.v2:run test_serialization.cpp  
./libs/uuid/test/Jamfile.v2:  : : : <library>/boost/serialization//boost_serialization <undefined-sanitizer>norecover:<link>static -$(WERROR) ;  
./libs/uuid/test/Jamfile.v2:# run test_wserialization.cpp /boost/serialization//boost_serialization /boost/serialization//boost_wserialization ;  
./libs/graph_parallel/test/Jamfile.v2:  [ mpi-test process_group_serialization : : : 2 ]  
./libs/core/test/Jamfile.v2:local with-serialization =  
./libs/core/test/Jamfile.v2:    <library>/boost/serialization//boost_serialization/<warnings>off  
./libs/core/test/Jamfile.v2:run serialization_nvp_test.cpp : : : $(with-serialization) <undefined-sanitizer>norecover:<build>no ;  
./libs/core/test/Jamfile.v2:run serialization_split_free_test.cpp : : : $(with-serialization) ;  
./libs/core/test/Jamfile.v2:run serialization_split_member_test.cpp : : : $(with-serialization) ;  
./libs/core/test/Jamfile.v2:run serialization_construct_data_test.cpp : : : $(with-serialization) ;  
./libs/bimap/example/Jamfile.v2:    [ run     bimap_and_boost/serialization.cpp  
./libs/bimap/example/Jamfile.v2:                    /boost/serialization//boost_serialization         ]  
./libs/bimap/test/Jamfile.v2:    [ run test_bimap_serialization.cpp  
./libs/bimap/test/Jamfile.v2:          /boost/serialization//boost_serialization                           ]  
./libs/bimap/test/Jamfile.v2:    [ compile ../example/bimap_and_boost/serialization.cpp  
./libs/bimap/test/Jamfile.v2:                    /boost/serialization//boost_serialization                 ]  
./libs/msm/example/mpl_graph/Jamfile.v2:       <library>/boost/serialization//boost_serialization/<link>static  
./libs/msm/test/Jamfile.v2:       <library>/boost/serialization//boost_serialization/<link>static  
./libs/asio/doc/Jamfile.v2:  cpp11/porthopper cpp11/serialization cpp11/services cpp11/socks4 cpp11/spawn  
./libs/asio/example/cpp11/serialization/Jamfile.v2:    <library>/boost/serialization//boost_serialization  
./libs/bloom/example/Jamfile.v2:exe serialization  
./libs/bloom/example/Jamfile.v2:  : serialization.cpp   
./libs/mpi/build/Jamfile.v2:    <library>/boost/serialization//boost_serialization  
./libs/mpi/build/Jamfile.v2:    <library>/boost/serialization//boost_serialization  
./libs/serialization/build/Jamfile.v2:# Boost serialization Library Build Jamfile  
./libs/serialization/build/Jamfile.v2:#  See http://www.boost.org/libs/serialization for the library home page.  
./libs/serialization/build/Jamfile.v2:project boost/serialization  
./libs/serialization/build/Jamfile.v2:lib boost_serialization   
./libs/serialization/build/Jamfile.v2:lib boost_wserialization   
./libs/serialization/build/Jamfile.v2:    : $(WSOURCES).cpp boost_serialization   
./libs/serialization/build/Jamfile.v2:    # note: both serialization and wserialization are conditioned on the this  
./libs/serialization/build/Jamfile.v2:boost-install boost_serialization boost_wserialization ;  
./libs/serialization/performance/xml/Jamfile.v2:                   : <toolset>gcc:<linkflags>"-lrt -lboost_serialization" ;  
./libs/serialization/performance/xml/Jamfile.v2:                   : <toolset>gcc:<linkflags>"-lrt -lboost_serialization" ;  
./libs/serialization/performance/xml/Jamfile.v2:                   : <toolset>gcc:<linkflags>"-lrt -lboost_serialization" ;  
./libs/serialization/performance/xml/Jamfile.v2:                   : <toolset>gcc:<linkflags>"-lrt -lboost_serialization" ;  
./libs/serialization/performance/xml/Jamfile.v2:                   : <toolset>gcc:<linkflags>"-lrt -lboost_serialization" ;  
./libs/serialization/performance/xml/Jamfile.v2:                   : <toolset>gcc:<linkflags>"-lrt -lboost_serialization" ;  
./libs/serialization/performance/xml/Jamfile.v2:                   : <toolset>gcc:<linkflags>"-lrt -lboost_serialization" ;  
./libs/serialization/performance/xml/Jamfile.v2:                   : <toolset>gcc:<linkflags>"-lrt -lboost_serialization" ;  
./libs/serialization/performance/Jamfile.v2:# Boost serialization Library performance test Jamfile  
./libs/serialization/performance/Jamfile.v2:project libs/serialization/performance  
./libs/serialization/performance/Jamfile.v2:    : id serialization_performance  
./libs/serialization/performance/Jamfile.v2:# import rules from the boost serialization test  
./libs/serialization/example/Jamfile.v2:# Boost serialization Library Build Jamfile  
./libs/serialization/example/Jamfile.v2:#  See http://www.boost.org/libs/serialization for the library home page.  
./libs/serialization/example/Jamfile.v2:project libs/serialization/example  
./libs/serialization/example/Jamfile.v2:    : id serialization_example  
./libs/serialization/example/Jamfile.v2:    : requirements <library>../build//boost_serialization  
./libs/serialization/test/Jamfile.v2:# Boost serialization Library test Jamfile  
./libs/serialization/test/Jamfile.v2:project libs/serialization/test  
./libs/serialization/test/Jamfile.v2:    : id serialization_test  
./libs/serialization/test/Jamfile.v2:# import rules from the boost serialization test  
./libs/serialization/test/Jamfile.v2:        ../build//boost_serialization  
./libs/serialization/test/Jamfile.v2:        ../build//boost_serialization  
./libs/serialization/test/Jamfile.v2:        ../build//boost_serialization  
./libs/serialization/test/Jamfile.v2:test-suite "serialization" :  
./libs/serialization/test/Jamfile.v2:    test-suite "serialization2" :   
./libs/graph/example/Jamfile.v2:# run kevin-bacon2.cpp /boost/serialization//boost_serialization : $(TEST_DIR)/kevin-bacon.dat ;  
./libs/graph/test/Jamfile.v2:          ../../serialization/build//boost_serialization  
./libs/math/example/Jamfile.v2:      #  because serialization library is built with MS extensions enabled (default).  
./libs/math/example/Jamfile.v2:   #[ run nonfinite_num_facet_serialization.cpp /boost/serialization//boost_serialization : : : <exception-handling>off:<build>no <toolset>gcc-mingw:<link>static  ]  
./libs/math/example/Jamfile.v2:   #[ run nonfinite_serialization_archives.cpp /boost/serialization//boost_serialization  : : : <exception-handling>off:<build>no <toolset>gcc-mingw:<link>static  ]  
```  
  
From my side, I have a list of libraries in Boost are not header-only by default:  
  
```  
- atomic  
- charconv  
- chrono  
- cobalt  
- container  
- context  
- contract  
- coroutine  
- date_time  
- exception  
- fiber  
- filesystem  
- graph  
- graph_parallel  
- iostreams  
- json  
- locale  
- log  
- math  
- mpi  
- nowide  
- process  
- program_options  
- python  
- random  
- regex  
- serialization  
- stacktrace  
- test  
- thread  
- timer  
- type_erasure  
- url  
- wave  
```  
  
As you can see, only `date_time` and `wave` from the grep are built as a regular library, all other are header-only. I also see some findings from examples and tests, no sure if they are built by default and are triggering that behavior.

---

## Comment 7

> Username: uilianries  
> Created at: 2025-11-04 12:04:37 UTC  
> Url: https://github.com/boostorg/serialization/issues/335#issuecomment-3485648028  

> Why is it important for you that Serialization not be built?  
  
The importance resides on customer's side, when trying to obtain a minimal Boost package; As Boost covers several modules, but not all are really required when developing a project, so it's correct to assume having a smaller Boost flavor.   
  
BTW, I had a similar problem a few weeks ago (https://github.com/boostorg/boost/issues/1051), where a transitive dependency was resulting in the generation of some shared libraries when it was not supposed to.

---

## Comment 8

> Username: pdimov  
> Created at: 2025-11-04 12:08:23 UTC  
> Url: https://github.com/boostorg/serialization/issues/335#issuecomment-3485660624  

Your grep misses `Jamfile` (without the `.v2`) and `build.jam`.

---

## Comment 9

> Username: pdimov  
> Created at: 2025-11-04 16:16:08 UTC  
> Url: https://github.com/boostorg/serialization/issues/335#issuecomment-3486829830  

So, does Serialization still get built when you exclude Log as well?

---

## Comment 10

> Username: uilianries  
> Created at: 2025-11-04 18:39:17 UTC  
> Updated at: 2025-11-04 18:40:17 UTC  
> Url: https://github.com/boostorg/serialization/issues/335#issuecomment-3487522599  

> So, does Serialization still get built when you exclude Log as well?  
  
@pdimov Good catch! Indeed it helps!  
  
I can avoid generating/installing serialiazion, but I need `--without-log` and `--without-wave` to make it work. The `--with-python` does not change the result either. Building only with `--without-log --without-log` does not work either.   
  
You can check my build result here:   
  
[boost-1.88.0-no-deps.log](https://github.com/user-attachments/files/23343598/boost-1.88.0-no-deps.log)
