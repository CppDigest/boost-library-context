# #1230 - boost/math 1.76 fails to build with C++17: `error: '__and_' was not declared in this scope; did you mean 'std::__and_'?` [Closed]

> Username: barracuda156  
> Created at: 2024-12-26 08:17:25 UTC  
> Updated at: 2024-12-26 09:46:36 UTC  
> Closed at: 2024-12-26 09:46:35 UTC  
> Url: https://github.com/boostorg/math/issues/1230  

While 1.76 is a legacy version, it is important to have working, since a lot of software uses it, and perhaps not everything can be easily switched to 1.81 or later Boost.  
  
Could this be fixed? It was working fine with C++11, but due to ICU requirements the port was moved to C++17.  
  
```  
"/opt/local/bin/g++-mp-14" -dynamiclib -Wl,-single_module -install_name "/opt/local/libexec/boost/1.76/lib/libboost_nowide-mt.dylib"  -o "bin.v2/libs/nowide/build/darwin-14.2.0/release/threading-multi/visibility-hidden/libboost_nowide-mt.dylib" "bin.v2/libs/nowide/build/darwin-14.2.0/release/threading-multi/visibility-hidden/cstdio.o" "bin.v2/libs/nowide/build/darwin-14.2.0/release/threading-multi/visibility-hidden/cstdlib.o" "bin.v2/libs/nowide/build/darwin-14.2.0/release/threading-multi/visibility-hidden/filebuf.o" "bin.v2/libs/nowide/build/darwin-14.2.0/release/threading-multi/visibility-hidden/iostream.o" "bin.v2/libs/nowide/build/darwin-14.2.0/release/threading-multi/visibility-hidden/stat.o"      -headerpad_max_install_names -fPIC -m32 -fvisibility=hidden -fvisibility-inlines-hidden -Wl,-dead_strip -no_dead_strip_inits_and_terms -L/opt/local/lib -Wl,-headerpad_max_install_names -Wl,-rpath,/opt/local/lib/libgcc  -arch ppc   
  
common.copy /opt/local/var/macports/build/_opt_local_var_macports_sources_rsync.macports.org_macports_release_tarballs_ports_devel_boost176/boost176/work/boost_1_76_0/stage/lib/libboost_nowide-mt.dylib  
  
    cp "bin.v2/libs/nowide/build/darwin-14.2.0/release/threading-multi/visibility-hidden/libboost_nowide-mt.dylib"  "/opt/local/var/macports/build/_opt_local_var_macports_sources_rsync.macports.org_macports_release_tarballs_ports_devel_boost176/boost176/work/boost_1_76_0/stage/lib/libboost_nowide-mt.dylib"  
  
common.mkdir bin.v2/libs/program_options  
  
        mkdir -p "bin.v2/libs/program_options"  
      
common.mkdir bin.v2/libs/program_options/build  
  
        mkdir -p "bin.v2/libs/program_options/build"  
      
common.mkdir bin.v2/libs/program_options/build/darwin-14.2.0  
  
        mkdir -p "bin.v2/libs/program_options/build/darwin-14.2.0"  
      
common.mkdir bin.v2/libs/program_options/build/darwin-14.2.0/release  
  
        mkdir -p "bin.v2/libs/program_options/build/darwin-14.2.0/release"  
      
common.mkdir bin.v2/libs/program_options/build/darwin-14.2.0/release/threading-multi  
  
        mkdir -p "bin.v2/libs/program_options/build/darwin-14.2.0/release/threading-multi"  
      
common.mkdir bin.v2/libs/program_options/build/darwin-14.2.0/release/threading-multi/visibility-hidden  
  
        mkdir -p "bin.v2/libs/program_options/build/darwin-14.2.0/release/threading-multi/visibility-hidden"  
      
darwin.compile.c++.pch bin.v2/libs/math/build/pch/darwin-14.2.0/release/threading-multi/visibility-hidden/../src/tr1/pch.hpp.gch  
  
    "/opt/local/bin/g++-mp-14" -x c++-header -fvisibility-inlines-hidden -Os -std=gnu++17 -Wno-enum-constexpr-conversion -Wno-unknown-warning-option -D_GLIBCXX_USE_CXX11_ABI=0 -arch ppc  -fPIC -m32 -O3 -Wall -fvisibility=hidden -dynamic -gdwarf-2 -fexceptions -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_BUILD_PCH_ENABLED -DBOOST_MATH_TR1_DYN_LINK=1 -DNDEBUG -I"." -I"libs/math/src/tr1" -c -o "bin.v2/libs/math/build/pch/darwin-14.2.0/release/threading-multi/visibility-hidden/../src/tr1/pch.hpp.gch" "libs/math/build/../src/tr1/pch.hpp"  
  
In file included from ./boost/math/tools/mp.hpp:341,  
                 from ./boost/math/policies/policy.hpp:11,  
                 from ./boost/math/special_functions/math_fwd.hpp:31,  
                 from ./boost/math/special_functions/airy.hpp:11,  
                 from ./boost/math/special_functions.hpp:15,  
                 from libs/math/build/../src/tr1/pch.hpp:9:  
/opt/local/include/gcc14/c++/utility:108:14: error: '__and_' was not declared in this scope; did you mean 'std::__and_'?  
  108 |     noexcept(__and_<is_nothrow_move_constructible<_Tp>,  
      |              ^~~~~~  
      |              std::__and_  
In file included from /opt/local/include/gcc14/c++/bits/stl_pair.h:60,  
                 from /opt/local/include/gcc14/c++/bits/stl_algobase.h:64,  
                 from /opt/local/include/gcc14/c++/vector:62,  
                 from ./boost/math/special_functions/math_fwd.hpp:26:  
/opt/local/include/gcc14/c++/type_traits:198:12: note: 'std::__and_' declared here  
  198 |     struct __and_  
      |            ^~~~~~  
/opt/local/include/gcc14/c++/utility:108:21: error: 'is_nothrow_move_constructible' was not declared in this scope; did you mean 'std::is_nothrow_move_constructible'?  
  108 |     noexcept(__and_<is_nothrow_move_constructible<_Tp>,  
      |                     ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
      |                     std::is_nothrow_move_constructible  
/opt/local/include/gcc14/c++/type_traits:1210:12: note: 'std::is_nothrow_move_constructible' declared here  
 1210 |     struct is_nothrow_move_constructible  
      |            ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/opt/local/include/gcc14/c++/utility:108:54: error: expected primary-expression before '>' token  
  108 |     noexcept(__and_<is_nothrow_move_constructible<_Tp>,  
      |                                                      ^  
/opt/local/include/gcc14/c++/utility:108:55: error: expected primary-expression before ',' token  
  108 |     noexcept(__and_<is_nothrow_move_constructible<_Tp>,  
      |                                                       ^  
/opt/local/include/gcc14/c++/utility:108:55: error: expected ')' before ',' token  
  108 |     noexcept(__and_<is_nothrow_move_constructible<_Tp>,  
      |             ~                                         ^  
      |                                                       )  
/opt/local/include/gcc14/c++/utility:108:55: error: expected ';' before ',' token  
  108 |     noexcept(__and_<is_nothrow_move_constructible<_Tp>,  
      |                                                       ^  
      |                                                       ;  
/opt/local/include/gcc14/c++/utility:116:15: error: 'add_const_t' does not name a type  
  116 |     constexpr add_const_t<_Tp>&  
      |               ^~~~~~~~~~~  
./boost/math/tools/mp.hpp:342:10: error: 'boost::math::tools::meta_programming::std::size_t' has not been declared  
  342 | template<std::size_t... I>  
      |          ^~~  
./boost/math/tools/mp.hpp:343:29: error: 'index_sequence' in namespace 'boost::math::tools::meta_programming::std' does not name a template type  
  343 | using index_sequence = std::index_sequence<I...>;  
      |                             ^~~~~~~~~~~~~~  
./boost/math/tools/mp.hpp:345:10: error: 'boost::math::tools::meta_programming::std::size_t' has not been declared  
  345 | template<std::size_t N>  
      |          ^~~  
./boost/math/tools/mp.hpp:346:34: error: 'make_index_sequence' in namespace 'boost::math::tools::meta_programming::std' does not name a template type  
  346 | using make_index_sequence = std::make_index_sequence<N>;  
      |                                  ^~~~~~~~~~~~~~~~~~~  
./boost/math/tools/mp.hpp:349:33: error: 'index_sequence_for' in namespace 'boost::math::tools::meta_programming::std' does not name a template type  
  349 | using index_sequence_for = std::index_sequence_for<T...>;  
      |                                 ^~~~~~~~~~~~~~~~~~  
cc1plus: note: unrecognized command-line option '-Wno-unknown-warning-option' may have been intended to silence earlier diagnostics  
cc1plus: note: unrecognized command-line option '-Wno-enum-constexpr-conversion' may have been intended to silence earlier diagnostics  
```  
  
See also: https://trac.macports.org/ticket/71647

---

## Comment 1

> Username: jzmaddock  
> Created at: 2024-12-26 09:19:23 UTC  
> Url: https://github.com/boostorg/math/issues/1230#issuecomment-2562354717  

I'm fairly sure that's not our issue: the first errors are coming from inside <type_traits> and I think we're just collateral damage here.  Looks like a compiler/std library mismatch or similar (older clang on top of newer GCC libstdc++)?

---

## Comment 2

> Username: barracuda156  
> Created at: 2024-12-26 09:24:59 UTC  
> Updated at: 2024-12-26 09:33:26 UTC  
> Url: https://github.com/boostorg/math/issues/1230#issuecomment-2562359513  

@jzmaddock This is gcc 14.2.0 with its native libgcc 14.2.0 (exactly matching versions). It is certainly possible the bug is in GCC though, however _Boost 1.81 builds fine_ on the same system with the same toolchain.  
  
Update. It is apparently a Boost bug: https://github.com/sccn/liblsl/issues/189#issuecomment-1501153750

---

## Comment 3

> Username: barracuda156  
> Created at: 2024-12-26 09:46:35 UTC  
> Url: https://github.com/boostorg/math/issues/1230#issuecomment-2562377027  

Well, yeah, it _was_ a Boost math bug, fixed in https://github.com/boostorg/math/commit/1ec5c98d80de97f9e962c5627e1a0e6096099894  
I got 1.76 building now after adding the patch.  
  
Closing, since AFAIK Boost does not backport fixes.
