# #167 - boost::nowide does not generate binaries when built as part of Boost 1.80 on macOS [Closed]

> Username: merwaaan  
> Created at: 2022-10-19 09:57:30 UTC  
> Updated at: 2022-10-20 08:41:49 UTC  
> Closed at: 2022-10-19 13:26:38 UTC  
> Url: https://github.com/boostorg/nowide/issues/167  

We're encountering issues building boost::nowide on macOS: **no binaries are produced**.  
  
Headers are installed, but there are no libraries nor CMake config to be found.  
Linking our own project against boost::nowide then fails because CMake is unable to find nowide's CMake config.  
  
We've been using various Boost libraries for some time now and this only happens for nowide.  
Building Boost the same way on Windows does produce binaries and linking succeeds.  
  
We're building Boost like this:  
  
```  
# Boost is a git submodule in our main project  
pushd boost/source  
  
./bootstrap.sh  
  
./b2 install \  
    --prefix=../../_boost_install \  
    --build-dir=../../_boost_build \  
    link=static runtime-link=static threading=multi address-model=64  
  
popd  
```  
  
Result:  
  
Headers are installed.  
```  
> ls _boost_install/include/boost/nowide   
  
args.hpp		convert.hpp		cstdlib.hpp		filebuf.hpp		fstream.hpp		replacement.hpp		stat.hpp		utf8_codecvt.hpp  
config.hpp		cstdio.hpp		detail			filesystem.hpp		iostream.hpp		stackstring.hpp		utf			windows.hpp  
```  
  
The library is missing.  
```  
> ls _boost_install/lib/  
cmake				libboost_contract.a		libboost_graph.a		libboost_program_options.a	libboost_stacktrace_noop.a	libboost_type_erasure.a  
libboost_atomic.a		libboost_coroutine.a		libboost_iostreams.a		libboost_random.a		libboost_system.a		libboost_unit_test_framework.a  
libboost_chrono.a		libboost_date_time.a		libboost_log.a			libboost_regex.a		libboost_test_exec_monitor.a	libboost_wserialization.a  
libboost_container.a		libboost_exception.a		libboost_log_setup.a		libboost_serialization.a	libboost_thread.a  
libboost_context.a		libboost_filesystem.a		libboost_prg_exec_monitor.a	libboost_stacktrace_basic.a	libboost_timer.a  
```  
  
The CMake config is missing.  
```  
> ls _boost_install/lib/cmake   
Boost-1.80.0				boost_coroutine-1.80.0			boost_iostreams-1.80.0			boost_regex-1.80.0			boost_timer-1.80.0  
BoostDetectToolset-1.80.0.cmake		boost_date_time-1.80.0			boost_log-1.80.0			boost_serialization-1.80.0		boost_type_erasure-1.80.0  
boost_atomic-1.80.0			boost_exception-1.80.0			boost_log_setup-1.80.0			boost_stacktrace_basic-1.80.0		boost_unit_test_framework-1.80.0  
boost_chrono-1.80.0			boost_filesystem-1.80.0			boost_mpi-1.80.0			boost_stacktrace_noop-1.80.0		boost_wserialization-1.80.0  
boost_container-1.80.0			boost_graph-1.80.0			boost_prg_exec_monitor-1.80.0		boost_system-1.80.0  
boost_context-1.80.0			boost_graph_parallel-1.80.0		boost_program_options-1.80.0		boost_test_exec_monitor-1.80.0  
boost_contract-1.80.0			boost_headers-1.80.0			boost_random-1.80.0			boost_thread-1.80.0  
```  
  
Are we doing something wrong @Flamefire ? Thanks

---

## Comment 1

> Username: Flamefire  
> Created at: 2022-10-19 12:16:29 UTC  
> Url: https://github.com/boostorg/nowide/issues/167#issuecomment-1283918919  

Boost.Nowide requires a "modern enough" Compiler, i.e. decent C++11 support, see https://github.com/boostorg/nowide/blob/75f7b516167c439f5f9786128a6942b7fc785ef4/build/Jamfile.v2#L24-L29. I suspect your compiler is to older or needs C++11 to be explicitly selected.  
  
Check the output of the configure checks of B2 if any of the checks linked above return "no" or attach the build log so I can have a look.

---

## Comment 2

> Username: merwaaan  
> Created at: 2022-10-19 13:26:37 UTC  
> Url: https://github.com/boostorg/nowide/issues/167#issuecomment-1284016636  

C++11 support was the issue indeed.  
  
```  
Performing configuration checks  
    ...  
    - cxx11_auto_declarations  : no [2]  
    - cxx11_constexpr          : no [2]  
    - cxx11_defaulted_functions : no [2]  
    - cxx11_final              : no [2]  
    - cxx11_hdr_mutex          : no [2]  
    - cxx11_hdr_tuple          : no [2]  
    - cxx11_lambdas            : no [2]  
    - cxx11_noexcept           : no [2]  
    - cxx11_nullptr            : no [2]  
    - cxx11_rvalue_references  : no [2]  
    - cxx11_template_aliases   : no [2]  
    - cxx11_thread_local       : no [2]  
    - cxx11_variadic_templates : no [2]  
    - cxx11_auto_declarations  : no [3]  
    - cxx11_constexpr          : no [3]  
    - cxx11_defaulted_functions : no [3]  
    - cxx11_final              : no [3]  
    - cxx11_hdr_mutex          : no [3]  
    - cxx11_hdr_tuple          : no [3]  
    - cxx11_lambdas            : no [3]  
    - cxx11_noexcept           : no [3]  
    - cxx11_nullptr            : no [3]  
    - cxx11_rvalue_references  : no [3]  
    - cxx11_template_aliases   : no [3]  
    - cxx11_thread_local       : no [3]  
    - cxx11_variadic_templates : no [3]  
```  
  
Explictly passing `cxxflags='-std=c++11'` to b2 fixed it. Otherwise, it fails silently.  
Thanks!

---

## Comment 3

> Username: Flamefire  
> Created at: 2022-10-20 08:41:48 UTC  
> Url: https://github.com/boostorg/nowide/issues/167#issuecomment-1285154216  

Note that you can make B2 do that for you `b2 cxxstd=11 ...`
