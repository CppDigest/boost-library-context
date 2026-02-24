# #362 - Can't build boost new versions (ASIO issue?) [Closed]

> Username: siliu-tacc  
> Created at: 2020-09-20 03:18:34 UTC  
> Updated at: 2020-12-30 01:14:37 UTC  
> Closed at: 2020-12-30 01:14:36 UTC  
> Url: https://github.com/boostorg/asio/issues/362  

Dear all,  
  
I got some issue to build the new versions of boost. The commands I used are:  
  
./bootstrap.sh --prefix=${MYINSTALL}  --with-toolset=intel-linux --with-libraries=all --without-libraries=mpi  
./b2 --prefix=${MYINSTALL}  -sICU_PATH=${MYINSTALL}  'cxxflags=${MYFLAG}'  'cflags=${MYFLAG}' 'linkflags=${MYFLAG}'  install  
  
The compilation can't be finished due to some instance of constructor issues.  
The related error message is attached at the end.  
  
Any suggestions are appreciated.  
Thanks a lot,  
  
  
  
  
./boost/asio/detail/io_object_impl.hpp(61): error: no instance of constructor "boost::asio::execution::any_executor<SupportableProperties...>::any_executor [with SupportableProperties=<boost::asio::execution::context_as_t<boost::asio::execution_context &>, boost::asio::execution::detail::blocking::never_t<0>, boost::asio::execution::prefer_only<boost::asio::execution::detail::blocking::possibly_t<0>>,  
          boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::tracked_t<0>>, boost::asio::execution::prefer_only<boost::asio::execution::detail::outstanding_work::untracked_t<0>>, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::fork_t<0>>, boost::asio::execution::prefer_only<boost::asio::execution::detail::relationship::continuation_t<0>>>]" matches the argument list  
            argument types are: (boost::asio::io_context::executor_type)  
        executor_(context.get_executor())  
                 ^  
./boost/asio/execution/any_executor.hpp(1409): note: this candidate was rejected because arguments do not match  
    any_executor(any_executor&& other) BOOST_ASIO_NOEXCEPT  
    ^  
./boost/asio/execution/any_executor.hpp(1382): note: this candidate was rejected because arguments do not match  
    any_executor(const any_executor& other) BOOST_ASIO_NOEXCEPT  
    ^  
./boost/asio/execution/any_executor.hpp(1363): note: this candidate was rejected because at least one template argument could not be deduced  
    any_executor(any_executor<OtherSupportableProperties...> other,  
    ^  
./boost/asio/execution/any_executor.hpp(1346): note: this candidate was rejected because at least one template argument could not be deduced  
    any_executor(Executor ex,  
    ^  
./boost/asio/execution/any_executor.hpp(1339): note: this candidate was rejected because arguments do not match  
    any_executor(nullptr_t) BOOST_ASIO_NOEXCEPT  
    ^  
./boost/asio/execution/any_executor.hpp(1333): note: this candidate was rejected because mismatch in count of arguments  
    any_executor() BOOST_ASIO_NOEXCEPT  
    ^  
          detected during:  
            instantiation of "boost::asio::detail::io_object_impl<IoObjectService, Executor>::io_object_impl(ExecutionContext &, std::enable_if<std::is_convertible<ExecutionContext &, boost::asio::execution_context &>::value, void>::type *) [with IoObjectService=boost::asio::detail::resolver_service<boost::asio::ip::udp>, Executor=boost::asio::any_io_executor, ExecutionContext=boost::asio::io_context]" at line 127 of "./boost/asio/ip/basic_resolver.hpp"  
            instantiation of "boost::asio::ip::basic_resolver<InternetProtocol, Executor>::basic_resolver(ExecutionContext &, std::enable_if<std::is_convertible<ExecutionContext &, boost::asio::execution_context &>::value, void>::type *) [with InternetProtocol=boost::asio::ip::udp, Executor=boost::asio::any_io_executor, ExecutionContext=boost::asio::io_context]" at line 366 of "libs/log/src/syslog_backend.cpp"  
  
compilation aborted for libs/log/src/syslog_backend.cpp (code 2)  
  
    "icpc" -c -xc++ -fvisibility-inlines-hidden -m64 -pthread -fvisibility=hidden -w1 -inline-level=2 -O3 -ip -wd177,780,2196,1782,193,304,981,1418,411,734,279 -xCORE-AVX-I -axCORE-AVX2 -xCORE-AVX-I -axCORE-AVX2 -DBOOST_ALL_NO_LIB=1 -DBOOST_ATOMIC_STATIC_LINK=1 -DBOOST_CHRONO_STATIC_LINK=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_HAS_ICU=1 -DBOOST_LOG_BUILDING_THE_LIB=1 -DBOOST_LOG_HAS_PTHREAD_MUTEX_ROBUST -DBOOST_LOG_USE_AVX2 -DBOOST_LOG_USE_NATIVE_SYSLOG -DBOOST_LOG_USE_SSSE3 -DBOOST_LOG_WITHOUT_DEBUG_OUTPUT -DBOOST_LOG_WITHOUT_EVENT_LOG -DBOOST_SPIRIT_USE_PHOENIX_V3=1 -DBOOST_THREAD_BUILD_LIB=1 -DBOOST_THREAD_DONT_USE_CHRONO=1 -DBOOST_THREAD_POSIX -DBOOST_THREAD_USE_LIB=1 -DNDEBUG -D_XOPEN_SOURCE=600 -D__STDC_CONSTANT_MACROS -I"." -I"/scratch/01255/siliu/BOOST/boost/1.74/include" -I"libs/log/src"   -c -o "bin.v2/libs/log/build/intel-linux/release/link-static/threadapi-pthread/threading-multi/visibility-hidden/syslog_backend.o" "libs/log/src/syslog_backend.cpp"  
  
...failed intel-linux.compile.c++ bin.v2/libs/log/build/intel-linux/release/link-static/threadapi-pthread/threading-multi/visibility-hidden/syslog_backend.o...  
...skipped <pbin.v2/libs/log/build/intel-linux/release/link-static/threadapi-pthread/threading-multi/visibility-hidden>libboost_log.a(clean) for lack of <pbin.v2/libs/log/build/intel-linux/release/link-static/threadapi-pthread/threading-multi/visibility-hidden>syslog_backend.o...

---

## Comment 1

> Username: siliu-tacc  
> Created at: 2020-09-28 14:28:12 UTC  
> Url: https://github.com/boostorg/asio/issues/362#issuecomment-700043391  

Any suggestions or recommendations?  
  
Thanks

---

## Comment 2

> Username: boegel  
> Created at: 2020-11-09 14:00:20 UTC  
> Url: https://github.com/boostorg/asio/issues/362#issuecomment-724030992  

I'm hitting the same problem when compiling Boost 1.74.0 with the latest Intel compilers (2020 update 4).  
  
This problem doesn't occur with Boost 1.72.0

---

## Comment 3

> Username: zao  
> Created at: 2020-11-09 14:46:02 UTC  
> Url: https://github.com/boostorg/asio/issues/362#issuecomment-724058240  

Building with `-DBOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT=1` seems to mitigate whatever problem there is with the executor property deduction machinery, but isn't a lasting solution.

---

## Comment 4

> Username: zao  
> Created at: 2020-11-09 14:47:19 UTC  
> Url: https://github.com/boostorg/asio/issues/362#issuecomment-724059009  

(flag found in https://www.boost.org/doc/libs/1_74_0/doc/html/boost_asio/std_executors.html )

---

## Comment 5

> Username: jbonyun  
> Created at: 2020-11-18 22:14:47 UTC  
> Url: https://github.com/boostorg/asio/issues/362#issuecomment-729990732  

This appears to be specific to the combination of the Intel compiler with newish GCC libstdc++. Because I don't have a problem with Clang 11 or GCC 10 when using libstdc++ from GCC 10. I don't have a problem with any of Clang/GCC/Intel when using older libstdc++ (definitely gcc 4.9.2, but I also think gcc 8.3.1). But I do have the problem when combining Intel 19.1.2 (from 2020 update 4 release), GCC 10's libstdc++, and boost 1.74.  
  
I can build with @zao's `-DBOOST_ASIO_USE_TS_EXECUTOR_AS_DEFAULT=1` work-around. No feedback on functionality using this define yet, but at least it compiles. Note that this define is when compiling your source code that uses asio, rather than when building boost itself (it's a header-only library anyway).

---

## Comment 6

> Username: ghost  
> Created at: 2020-12-30 01:14:35 UTC  
> Url: https://github.com/boostorg/asio/issues/362#issuecomment-752293696  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#765](https://github.com/chriskohlhoff/asio/issues/765).
