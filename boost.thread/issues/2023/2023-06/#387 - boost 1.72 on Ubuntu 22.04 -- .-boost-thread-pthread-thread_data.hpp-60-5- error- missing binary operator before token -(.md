# #387 - boost 1.72 on Ubuntu 22.04 -- ./boost/thread/pthread/thread_data.hpp:60:5: error: missing binary operator before token "(" [Closed]

> Username: monajalal  
> Created at: 2023-06-05 18:50:09 UTC  
> Updated at: 2023-06-06 20:34:56 UTC  
> Closed at: 2023-06-06 20:34:56 UTC  
> Url: https://github.com/boostorg/thread/issues/387  

I get this error trying to install boost 1.72 from source on Ubuntu 22.04  
Could you please suggest a fix?  
```  
boost-install.generate-cmake-config- bin.v2/libs/chrono/build/install/boost_chrono-config.cmake  
boost-install.generate-cmake-config-version- bin.v2/libs/chrono/build/install/boost_chrono-config-version.cmake  
common.copy /usr/local/include/boost/spirit/home/support/detail/lexer/parser/tree/selection_node.hpp  
common.copy /usr/local/include/boost/spirit/home/support/detail/lexer/parser/tree/node.hpp  
common.copy /usr/local/lib/libboost_context.so.1.72.0  
ln-UNIX /usr/local/lib/libboost_chrono.so  
ln-UNIX /usr/local/lib/libboost_chrono.so.1  
common.copy /usr/local/include/boost/spirit/home/support/detail/lexer/parser/tree/leaf_node.hpp  
...on 15700th target...  
boost-install.generate-cmake-config- bin.v2/libs/container/build/install/boost_container-config.cmake  
boost-install.generate-cmake-config-version- bin.v2/libs/container/build/install/boost_container-config-version.cmake  
ln-UNIX /usr/local/lib/libboost_container.so  
ln-UNIX /usr/local/lib/libboost_container.so.1  
ln-UNIX /usr/local/lib/libboost_container.so.1.72  
ln-UNIX /usr/local/lib/libboost_chrono.so.1.72  
common.copy /usr/local/lib/cmake/boost_headers-1.72.0/boost_headers-config-version.cmake  
common.copy /usr/local/include/boost/spirit/home/support/detail/lexer/parser/tree/iteration_node.hpp  
common.copy /usr/local/include/boost/spirit/home/support/detail/lexer/parser/tree/end_node.hpp  
common.copy /usr/local/include/boost/spirit/home/support/detail/lexer/parser/tokeniser/re_tokeniser_state.hpp  
common.copy /usr/local/include/boost/spirit/home/support/detail/lexer/parser/tokeniser/re_tokeniser.hpp  
common.copy /usr/local/include/boost/spirit/home/support/detail/lexer/parser/tokeniser/re_tokeniser_helper.hpp  
common.copy /usr/local/lib/cmake/boost_headers-1.72.0/boost_headers-config.cmake  
common.copy /usr/local/include/boost/spirit/home/support/detail/lexer/parser/tokeniser/num_token.hpp  
common.copy /usr/local/lib/cmake/boost_chrono-1.72.0/boost_chrono-config.cmake  
ln-UNIX /usr/local/lib/libboost_context.so  
common.copy /usr/local/lib/cmake/boost_chrono-1.72.0/boost_chrono-config-version.cmake  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/cwctype  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/cwchar  
common.copy /usr/local/lib/cmake/boost_container-1.72.0/boost_container-config-version.cmake  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/ctime  
common.copy /usr/local/lib/cmake/boost_container-1.72.0/boost_container-config.cmake  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/cstring  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/cstdlib  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/cstddef  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/cstdio  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/cstdarg  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/csignal  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/csetjmp  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/cmath  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/clocale  
...skipped <p/usr/local/lib>libboost_thread.so.1.72.0 for lack of <pbin.v2/libs/thread/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_thread.so.1.72.0...  
...skipped <p/usr/local/lib>libboost_thread.so for lack of <p/usr/local/lib>libboost_thread.so.1.72.0...  
...skipped <p/usr/local/lib>libboost_thread.so.1 for lack of <p/usr/local/lib>libboost_thread.so.1.72.0...  
...skipped <p/usr/local/lib>libboost_thread.so.1.72 for lack of <p/usr/local/lib>libboost_thread.so.1.72.0...  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/cerrno  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/climits  
...skipped <pbin.v2/libs/thread/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_thread-variant-shared.cmake for lack of <pbin.v2/libs/thread/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_thread.so.1.72.0...  
...skipped <p/usr/local/lib/cmake/boost_thread-1.72.0>libboost_thread-variant-shared.cmake for lack of <pbin.v2/libs/thread/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_thread-variant-shared.cmake...  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/cfloat  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/cctype  
ln-UNIX /usr/local/lib/libboost_context.so.1  
ln-UNIX /usr/local/lib/libboost_context.so.1.72  
common.copy /usr/local/include/boost/compatibility/cpp_c_headers/cassert  
common.copy /usr/local/lib/cmake/boost_context-1.72.0/libboost_context-variant-shared.cmake  
common.copy /usr/local/lib/cmake/boost_contract-1.72.0/libboost_contract-variant-shared.cmake  
common.copy /usr/local/lib/libboost_contract.so.1.72.0  
boost-install.generate-cmake-config- bin.v2/libs/context/build/install/boost_context-config.cmake  
boost-install.generate-cmake-config-version- bin.v2/libs/context/build/install/boost_context-config-version.cmake  
common.copy /usr/local/lib/cmake/boost_date_time-1.72.0/libboost_date_time-variant-shared.cmake  
common.copy /usr/local/lib/libboost_date_time.so.1.72.0  
boost-install.generate-cmake-config- bin.v2/libs/coroutine/build/install/boost_coroutine-config.cmake  
common.copy /usr/local/lib/libboost_exception.a  
boost-install.generate-cmake-config-version- bin.v2/libs/coroutine/build/install/boost_coroutine-config-version.cmake  
boost-install.generate-cmake-config- bin.v2/libs/contract/build/install/boost_contract-config.cmake  
ln-UNIX /usr/local/lib/libboost_date_time.so  
ln-UNIX /usr/local/lib/libboost_date_time.so.1  
ln-UNIX /usr/local/lib/libboost_date_time.so.1.72  
boost-install.generate-cmake-config- bin.v2/libs/thread/build/install/boost_thread-config.cmake  
boost-install.generate-cmake-config-version- bin.v2/libs/thread/build/install/boost_thread-config-version.cmake  
boost-install.generate-cmake-config- bin.v2/libs/exception/build/install/boost_exception-config.cmake  
boost-install.generate-cmake-config- bin.v2/libs/date_time/build/install/boost_date_time-config.cmake  
boost-install.generate-cmake-config-version- bin.v2/libs/date_time/build/install/boost_date_time-config-version.cmake  
ln-UNIX /usr/local/lib/libboost_contract.so  
ln-UNIX /usr/local/lib/libboost_contract.so.1  
ln-UNIX /usr/local/lib/libboost_contract.so.1.72  
common.copy /usr/local/lib/cmake/boost_context-1.72.0/boost_context-config.cmake  
common.copy /usr/local/lib/cmake/boost_contract-1.72.0/boost_contract-config.cmake  
common.copy /usr/local/lib/cmake/boost_context-1.72.0/boost_context-config-version.cmake  
boost-install.generate-cmake-config-version- bin.v2/libs/contract/build/install/boost_contract-config-version.cmake  
boost-install.generate-cmake-config-version- bin.v2/libs/exception/build/install/boost_exception-config-version.cmake  
common.copy /usr/local/lib/cmake/boost_coroutine-1.72.0/boost_coroutine-config.cmake  
common.copy /usr/local/lib/cmake/boost_coroutine-1.72.0/boost_coroutine-config-version.cmake  
common.copy /usr/local/lib/cmake/boost_thread-1.72.0/boost_thread-config-version.cmake  
common.copy /usr/local/lib/cmake/boost_thread-1.72.0/boost_thread-config.cmake  
common.copy /usr/local/lib/cmake/boost_exception-1.72.0/boost_exception-config.cmake  
common.copy /usr/local/lib/cmake/boost_date_time-1.72.0/boost_date_time-config.cmake  
common.copy /usr/local/lib/cmake/boost_date_time-1.72.0/boost_date_time-config-version.cmake  
common.copy /usr/local/lib/cmake/boost_contract-1.72.0/boost_contract-config-version.cmake  
common.copy /usr/local/lib/cmake/boost_exception-1.72.0/boost_exception-config-version.cmake  
gcc.compile.c++ bin.v2/libs/log/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden/setup/init_from_settings.o  
In file included from /usr/include/x86_64-linux-gnu/bits/local_lim.h:81,  
                 from /usr/include/x86_64-linux-gnu/bits/posix1_lim.h:161,  
                 from /usr/include/limits.h:195,  
                 from /usr/lib/gcc/x86_64-linux-gnu/11/include/limits.h:203,  
                 from /usr/lib/gcc/x86_64-linux-gnu/11/include/syslimits.h:7,  
                 from /usr/lib/gcc/x86_64-linux-gnu/11/include/limits.h:34,  
                 from ./boost/log/detail/config.hpp:33,  
                 from ./boost/log/detail/setup_config.hpp:20,  
                 from libs/log/src/setup/init_from_settings.cpp:26:  
./boost/thread/pthread/thread_data.hpp:60:5: error: missing binary operator before token "("  
   60 | #if PTHREAD_STACK_MIN > 0  
      |     ^~~~~~~~~~~~~~~~~  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -ftemplate-depth-1024 -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_DYN_LINK=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_DATE_TIME_DYN_LINK=1 -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_HAS_ICU=1 -DBOOST_LOG_DYN_LINK=1 -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_SETUP_BUILDING_THE_LIB=1 -DBOOST_LOG_SETUP_DLL -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DDATE_TIME_INLINE -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"/usr/include" -I"libs/log/src" -c -o "bin.v2/libs/log/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden/setup/init_from_settings.o" "libs/log/src/setup/init_from_settings.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/log/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden/setup/init_from_settings.o...  
...skipped <pbin.v2/libs/log/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup.so.1.72.0 for lack of <pbin.v2/libs/log/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>setup/init_from_settings.o...  
...skipped <p/usr/local/lib>libboost_log_setup.so.1.72.0 for lack of <pbin.v2/libs/log/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup.so.1.72.0...  
...skipped <p/usr/local/lib>libboost_log_setup.so for lack of <p/usr/local/lib>libboost_log_setup.so.1.72.0...  
...skipped <p/usr/local/lib>libboost_log_setup.so.1 for lack of <p/usr/local/lib>libboost_log_setup.so.1.72.0...  
...skipped <p/usr/local/lib>libboost_log_setup.so.1.72 for lack of <p/usr/local/lib>libboost_log_setup.so.1.72.0...  
...skipped <pbin.v2/libs/log/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup-variant-shared.cmake for lack of <pbin.v2/libs/log/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup.so.1.72.0...  
...skipped <p/usr/local/lib/cmake/boost_log_setup-1.72.0>libboost_log_setup-variant-shared.cmake for lack of <pbin.v2/libs/log/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup-variant-shared.cmake...  
gcc.compile.c++ bin.v2/libs/coroutine/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden/posix/stack_traits.o  
In file included from /usr/include/pthread.h:33,  
                 from /usr/include/x86_64-linux-gnu/c++/11/bits/gthr-default.h:35,  
                 from /usr/include/x86_64-linux-gnu/c++/11/bits/gthr.h:148,  
                 from /usr/include/c++/11/ext/atomicity.h:35,  
                 from /usr/include/c++/11/bits/basic_string.h:39,  
                 from /usr/include/c++/11/string:55,  
                 from ./boost/thread/exceptions.hpp:20,  
                 from ./boost/thread/pthread/thread_data.hpp:10,  
                 from ./boost/thread/thread_only.hpp:17,  
                 from ./boost/thread/thread.hpp:12,  
                 from ./boost/thread.hpp:13,  
                 from libs/coroutine/src/posix/stack_traits.cpp:22:  
./boost/thread/pthread/thread_data.hpp:60:5: error: missing binary operator before token "("  
   60 | #if PTHREAD_STACK_MIN > 0  
      |     ^~~~~~~~~~~~~~~~~  
  
    "g++"   -fvisibility-inlines-hidden -fPIC -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden  -DBOOST_ALL_NO_LIB=1 -DBOOST_CHRONO_DYN_LINK=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_COROUTINES_DYN_LINK=1 -DBOOST_COROUTINES_SOURCE -DBOOST_DISABLE_ASSERTS -DBOOST_THREAD_BUILD_DLL=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_DLL=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/coroutine/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden/posix/stack_traits.o" "libs/coroutine/src/posix/stack_traits.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/coroutine/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden/posix/stack_traits.o...  
...skipped <pbin.v2/libs/coroutine/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_coroutine.so.1.72.0 for lack of <pbin.v2/libs/coroutine/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>posix/stack_traits.o...  
...skipped <p/usr/local/lib>libboost_coroutine.so.1.72.0 for lack of <pbin.v2/libs/coroutine/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_coroutine.so.1.72.0...  
...skipped <p/usr/local/lib>libboost_coroutine.so for lack of <p/usr/local/lib>libboost_coroutine.so.1.72.0...  
...skipped <p/usr/local/lib>libboost_coroutine.so.1 for lack of <p/usr/local/lib>libboost_coroutine.so.1.72.0...  
...skipped <p/usr/local/lib>libboost_coroutine.so.1.72 for lack of <p/usr/local/lib>libboost_coroutine.so.1.72.0...  
...skipped <pbin.v2/libs/coroutine/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_coroutine-variant-shared.cmake for lack of <pbin.v2/libs/coroutine/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_coroutine.so.1.72.0...  
...skipped <p/usr/local/lib/cmake/boost_coroutine-1.72.0>libboost_coroutine-variant-shared.cmake for lack of <pbin.v2/libs/coroutine/build/gcc-11/release/threadapi-pthread/threading-multi/visibility-hidden>libboost_coroutine-variant-shared.cmake...  
gcc.compile.c++ bin.v2/libs/log/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden/setup/init_from_settings.o  
In file included from /usr/include/x86_64-linux-gnu/bits/local_lim.h:81,  
                 from /usr/include/x86_64-linux-gnu/bits/posix1_lim.h:161,  
                 from /usr/include/limits.h:195,  
                 from /usr/lib/gcc/x86_64-linux-gnu/11/include/limits.h:203,  
                 from /usr/lib/gcc/x86_64-linux-gnu/11/include/syslimits.h:7,  
                 from /usr/lib/gcc/x86_64-linux-gnu/11/include/limits.h:34,  
                 from ./boost/log/detail/config.hpp:33,  
                 from ./boost/log/detail/setup_config.hpp:20,  
                 from libs/log/src/setup/init_from_settings.cpp:26:  
./boost/thread/pthread/thread_data.hpp:60:5: error: missing binary operator before token "("  
   60 | #if PTHREAD_STACK_MIN > 0  
      |     ^~~~~~~~~~~~~~~~~  
  
    "g++"   -fvisibility-inlines-hidden -m64 -pthread -O3 -finline-functions -Wno-inline -Wall -fvisibility=hidden -fno-strict-aliasing -ftemplate-depth-1024 -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_HAS_ICU=1 -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_SETUP_BUILDING_THE_LIB=1 -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_LIB=1 -DDATE_TIME_INLINE -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS  -I"." -I"/usr/include" -I"libs/log/src" -c -o "bin.v2/libs/log/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden/setup/init_from_settings.o" "libs/log/src/setup/init_from_settings.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/log/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden/setup/init_from_settings.o...  
...skipped <pbin.v2/libs/log/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup.a(clean) for lack of <pbin.v2/libs/log/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden>setup/init_from_settings.o...  
...skipped <pbin.v2/libs/log/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup.a for lack of <pbin.v2/libs/log/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden>setup/init_from_settings.o...  
...skipped <p/usr/local/lib>libboost_log_setup.a for lack of <pbin.v2/libs/log/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup.a...  
...skipped <pbin.v2/libs/log/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup-variant-static.cmake for lack of <pbin.v2/libs/log/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup.a...  
...skipped <p/usr/local/lib/cmake/boost_log_setup-1.72.0>libboost_log_setup-variant-static.cmake for lack of <pbin.v2/libs/log/build/gcc-11/release/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log_setup-variant-static.cmake...  
...failed updating 20 targets...  
...skipped 79 targets...  
...updated 15753 targets...  
  
```  
  
  
```  
2007  tar -xvzf boost_1_72_0.tar.gz   
 2008  cd boost_1_72_0/  
 2013  ./bootstrap.sh   
 2014  ./b2  
 2015  sudo ./b2 install  
 2016  history  
```
