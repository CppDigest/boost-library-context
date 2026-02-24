# #222 - Boost requires TBB? [Closed]

> Username: Baksling  
> Created at: 2023-12-13 11:58:13 UTC  
> Updated at: 2024-08-07 11:18:56 UTC  
> Closed at: 2024-08-07 11:18:56 UTC  
> Url: https://github.com/boostorg/unordered/issues/222  

When installing version 1.83.0 and including <boost/unordered/concurrent_flat_map.h>, i get an error that there is an undefined reference to TBB oneapi aka:  
  
/usr/bin/ld: tests/CMakeFiles/parser.dir/parser_test.cpp.o: in function `tbb::detail::d1::execution_slot(tbb::detail::d1::execution_data const&)':  
/usr/include/oneapi/tbb/detail/_task.h:169: undefined reference to `tbb::detail::r1::execution_slot(tbb::detail::d1::execution_data const*)'  
/usr/bin/ld: tests/CMakeFiles/parser.dir/parser_test.cpp.o: in function `tbb::detail::d1::current_thread_index()':  
/usr/include/oneapi/tbb/task_arena.h:454: undefined reference to `tbb::detail::r1::execution_slot(tbb::detail::d1::execution_data const*)'  
  
I am unable to find in the documentation that concurrent_flat_map relies on TBB, but i might be missing some build options?  
I am using the following command to download and install boost to my project, where i rely on the program_options and test:  
  
ExternalProject_Add(  
            boost-ext  
            URL https://boostorg.jfrog.io/artifactory/main/release/1.83.0/source/boost_1_83_0.zip  
            URL_HASH SHA256=c86bd9d9eef795b4b0d3802279419fde5221922805b073b9bd822edecb1ca28e  
            PATCH_COMMAND echo "${BOOST_CONFIG_JAM}" $<SEMICOLON>  > config.jam  
            CONFIGURE_COMMAND ./bootstrap.sh --with-toolset=${BOOST_TOOLSET} --prefix=${CMAKE_BINARY_DIR}/external cxxflags="-arch x86_64" --without-icu  
            BUILD_COMMAND ./b2 --user-config=config.jam --prefix=${EXTERNAL_INSTALL_LOCATION} --with-test --with-program_options cxxstd=17 cflags="-fPIC" cxxflags="-fPIC" address-model=64 ${BOOST_B2_OPTS} variant=release link=shared runtime-link=shared install  
            BUILD_IN_SOURCE 1  
            INSTALL_COMMAND ""  
    )  
      
    Am i missing an option or component for concurrent_flat_map? I have tried adding --with-containers and --with-unordered, but the issues remain

---

## Comment 1

> Username: pdimov  
> Created at: 2023-12-13 14:37:33 UTC  
> Url: https://github.com/boostorg/unordered/issues/222#issuecomment-1854035951  

What compiler and platform is this?

---

## Comment 2

> Username: pdimov  
> Created at: 2023-12-13 14:43:33 UTC  
> Url: https://github.com/boostorg/unordered/issues/222#issuecomment-1854046721  

Some versions of GCC (e.g. GCC 9) implement the standard `<execution>` header on top of TBB.  
  
https://godbolt.org/z/PvKo5KEzc  
  
If that's your issue, you can try to define `BOOST_UNORDERED_DISABLE_PARALLEL_ALGORITHMS` and see if it helps.  
  
https://github.com/boostorg/unordered/blob/f493603f5cd2bd090be440cacfcaa95f2566059b/include/boost/unordered/detail/foa/concurrent_table.hpp#L41
