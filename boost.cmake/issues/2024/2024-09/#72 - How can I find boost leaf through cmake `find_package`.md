# #72 - [Question] How can I find boost leaf through cmake `find_package`? [Closed]

> Username: lidongze0629  
> Created at: 2024-09-04 12:03:42 UTC  
> Updated at: 2024-09-05 03:02:21 UTC  
> Closed at: 2024-09-05 03:02:21 UTC  
> Url: https://github.com/boostorg/cmake/issues/72  

I installed boost 1.75 by:  
  
```bash  
$ ./bootstrap.sh [--with-libraries=system,filesystem,context,program_options,regex,thread,random,chrono,atomic,date_time]  
$ ./b2 install link=shared runtime-link=shared variant=release threading=multi  
  
# ls /usr/lib/cmake  
Boost-1.75.0                     boost_fiber-1.75.0           boost_log_setup-1.75.0         boost_program_options-1.75.0           boost_system-1.75.0  
BoostDetectToolset-1.75.0.cmake  boost_fiber_numa-1.75.0      boost_math_c99-1.75.0          boost_random-1.75.0                    boost_test_exec_monitor-1.75.0  
boost_atomic-1.75.0              boost_filesystem-1.75.0      boost_math_c99f-1.75.0         boost_regex-1.75.0                     boost_thread-1.75.0  
boost_chrono-1.75.0              boost_graph-1.75.0           boost_math_c99l-1.75.0         boost_serialization-1.75.0             boost_timer-1.75.0  
boost_container-1.75.0           boost_graph_parallel-1.75.0  boost_math_tr1-1.75.0          boost_stacktrace_addr2line-1.75.0      boost_type_erasure-1.75.0  
boost_context-1.75.0             boost_headers-1.75.0         boost_math_tr1f-1.75.0         boost_stacktrace_backtrace-1.75.0      boost_unit_test_framework-1.75.0  
boost_contract-1.75.0            boost_iostreams-1.75.0       boost_math_tr1l-1.75.0         boost_stacktrace_basic-1.75.0          boost_wave-1.75.0  
boost_coroutine-1.75.0           boost_json-1.75.0            boost_mpi-1.75.0               boost_stacktrace_noop-1.75.0           boost_wserialization-1.75.0  
boost_date_time-1.75.0           boost_locale-1.75.0          boost_nowide-1.75.0            boost_stacktrace_windbg-1.75.0  
boost_exception-1.75.0           boost_log-1.75.0             boost_prg_exec_monitor-1.75.0  boost_stacktrace_windbg_cached-1.75.0  
```  
  
However, I can't find leaf module in my `CMakeLists.txt`  
  
```  
# CMakeLists.txt  
find_package(Boost COMPONENTS leaf)  
  
$ cmake ..  
-- Could NOT find Boost: missing: leaf (found /usr/local/lib/cmake/Boost-1.75.0/BoostConfig.cmake (found version "1.75.0"))  
```  
  
Does anyone knows how to find boost leaf through `find_package`, thanks very much.

---

## Comment 1

> Username: pdimov  
> Created at: 2024-09-04 13:59:35 UTC  
> Url: https://github.com/boostorg/cmake/issues/72#issuecomment-2329150076  

LEAF is a header-only library, so it doesn't have a component. Use just `find_package(Boost)` and then link to `Boost::headers`.
