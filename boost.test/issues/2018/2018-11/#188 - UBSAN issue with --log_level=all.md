# #188 - UBSAN issue with --log_level=all [Closed]

> Username: jeking3  
> Created at: 2018-11-21 13:44:48 UTC  
> Updated at: 2019-08-01 21:42:17 UTC  
> Closed at: 2019-07-22 10:49:10 UTC  
> Url: https://github.com/boostorg/test/issues/188  

Was compiling Boost.Thread for UBSAN and ran into a problem with a test.  I wasn't sure which test it was so I tried running the test with --log_level=all, and I saw a different error in Boost.Test.  
  
This is reproducible in the [Boost Docker Development Environment](https://github.com/boostorg/boost-ci).  To reproduce first we build the test for ubsan and see an issue for Boost.Thread (not this issue - keep reading - this issue is after):  
```  
jking@ubuntu:~/boost/libs/thread/test$ bdde  
docker run -v /home/jking/boost:/boost:rw -v /home/jking/bdde:/bdde:ro --workdir /boost/libs/thread/test -it jeking3/bdde:linux /bin/bash  
boost@a84a7e1cdcec:/boost/libs/thread/test$ ubsan test_thread_exit  
UBSAN_OPTIONS=print_stacktrace=1 b2 define=BOOST_NO_STRESS_TEST=1 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug toolset=gcc-8 test_thread_exit  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : yes (cached)  
    - lockfree boost::atomic_flag : yes (cached)  
...patience...  
...patience...  
...found 3380 targets...  
...updating 2 targets...  
testing.capture-output ../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.run  
====== BEGIN OUTPUT ======  
../../../libs/thread/src/pthread/thread.cpp:103:58: runtime error: member call on address 0x7f44d0000b20 which does not point to an object of type 'thread_exit_function_base'  
0x7f44d0000b20: note: object is of type 'boost::detail::thread_exit_function<void (*)()>'  
 00 00 00 00  b0 4f 0a ce 15 56 00 00  cb 07 07 ce 15 56 00 00  00 00 00 00 00 00 00 00  25 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::detail::thread_exit_function<void (*)()>'  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/boost/bin.v2/libs/chrono/build/gcc-8/debug/threading-multi/visibility-hidden:/boost/bin.v2/libs/system/build/gcc-8/debug/threading-multi/visibility-hidden:/boost/bin.v2/libs/test/build/gcc-8/debug/threading-multi/visibility-hidden:/boost/bin.v2/libs/thread/build/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden:/boost/bin.v2/libs/timer/build/gcc-8/debug/threading-multi/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit"   > "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output" "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.run...  
...failed updating 1 target...  
...skipped 1 target...  
```  
Now we run the test with --log_level=all and see this issue:  
```  
boost@a84a7e1cdcec:/boost/libs/thread/test$ UBSAN_OPTIONS=print_stacktrace=1 ../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit --log_level=all  
Running 2 test cases...  
Entering test module "Boost.Threads: thread exit test suite"  
test_thread_exit.cpp(32): Entering test case "test_thread_exit_func_runs_when_thread_exits"  
../../../boost/test/impl/unit_test_log.ipp:451:134: runtime error: member call on address 0x7f423a21adf0 which does not point to an object of type 'lazy_ostream'  
0x7f423a21adf0: note: object is of type 'boost::unit_test::lazy_ostream_impl<boost::unit_test::lazy_ostream, boost::unit_test::basic_cstring<char const>, boost::unit_test::basic_cstring<char const> const&>'  
 00 00 00 00  28 30 47 57 9f 55 00 00  00 aa 46 57 9f 55 00 00  40 02 49 57 9f 55 00 00  b0 ad 21 3a  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::unit_test::lazy_ostream_impl<boost::unit_test::lazy_ostream, boost::unit_test::basic_cstring<char const>, boost::unit_test::basic_cstring<char const> const&>'  
    #0 0x7f423c5fd4b3 in boost::unit_test::unit_test_log_t::operator<<(boost::unit_test::lazy_ostream const&) ../../../boost/test/impl/unit_test_log.ipp:451  
    #1 0x7f423c5ca8cc in void boost::test_tools::tt_detail::format_report<boost::unit_test::unit_test_log_t>(boost::unit_test::unit_test_log_t&, boost::test_tools::assertion_result const&, boost::unit_test::lazy_ostream const&, boost::test_tools::tt_detail::tool_level, boost::test_tools::tt_detail::check_type, unsigned long, __va_list_tag*, char const*, char const*) ../../../boost/test/impl/test_tools.ipp:144  
    #2 0x7f423c5bf9fa in boost::test_tools::tt_detail::report_assertion(boost::test_tools::assertion_result const&, boost::unit_test::lazy_ostream const&, boost::unit_test::basic_cstring<char const>, unsigned long, boost::test_tools::tt_detail::tool_level, boost::test_tools::tt_detail::check_type, unsigned long, ...) ../../../boost/test/impl/test_tools.ipp:355  
    #3 0x559f5743eaea in tf1() /boost/libs/thread/test/test_thread_exit.cpp:29  
    #4 0x559f57467b5a in boost::detail::thread_data<void (*)()>::run() ../../../boost/thread/detail/thread.hpp:117  
    #5 0x7f423ca6f307 in thread_proxy ../../../libs/thread/src/pthread/thread.cpp:177  
    #6 0x7f423adbe6da in start_thread (/lib/x86_64-linux-gnu/libpthread.so.0+0x76da)  
    #7 0x7f423aae788e in __clone (/lib/x86_64-linux-gnu/libc.so.6+0x12188e)  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2019-01-10 07:37:37 UTC  
> Url: https://github.com/boostorg/test/issues/188#issuecomment-452998683  

Digging into this and similar errors reported from UBSAN: it seems to be an issue caused by the visibility changes.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2019-02-02 12:58:05 UTC  
> Url: https://github.com/boostorg/test/issues/188#issuecomment-459963204  

I believe this is now fixed on develop. Would it be possible for you to retest? thanks!

---

## Comment 3

> Username: jeking3  
> Created at: 2019-02-03 15:52:06 UTC  
> Url: https://github.com/boostorg/test/issues/188#issuecomment-460063171  

I am doing this now - just a note the instructions given make it quite easy to verify with the Boost Docker Development Environment.  Two commands and you're running it in UBSAN.

---

## Comment 4

> Username: jeking3  
> Created at: 2019-02-03 15:52:51 UTC  
> Url: https://github.com/boostorg/test/issues/188#issuecomment-460063239  

I am getting this:  
```  
gcc.compile.c++ ../../bin.v2/libs/test/build/gcc-7.3.0/debug/threading-multi/visibility-hidden/framework.o  
In file included from ../../boost/test/impl/framework.ipp:51:0,  
                 from ../../libs/test/src/framework.cpp:16:  
../../boost/timer.hpp:21:70: note: #pragma message: This header is deprecated. Use the facilities in <boost/timer/timer.hpp> instead.  
 BOOST_HEADER_DEPRECATED( "the facilities in <boost/timer/timer.hpp>" )  
                                                                      ^  
```  
  
Something to be aware of... I just sync'd to develop.

---

## Comment 5

> Username: jeking3  
> Created at: 2019-02-03 16:29:38 UTC  
> Url: https://github.com/boostorg/test/issues/188#issuecomment-460066255  

Still failing:  
```  
jking@ubuntu:~/boost/libs/thread/test$ bdde  
docker run --cap-add=SYS_PTRACE --security-opt seccomp=unconfined -v /home/jking/boost:/boost:rw -v /home/jking/bdde:/bdde:ro --workdir /boost/libs/thread/test -it jeking3/bdde:linux /bin/bash  
boost@1efda23852f2:/boost/libs/thread/test$ ubsan test_thread_exit  
UBSAN_OPTIONS=print_stacktrace=1 b2 define=BOOST_NO_STRESS_TEST=1 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug toolset=gcc-8 test_thread_exit  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : yes (cached)  
    - lockfree boost::atomic_flag : yes (cached)  
...patience...  
...patience...  
...found 3375 targets...  
...updating 2 targets...  
testing.capture-output ../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.run  
====== BEGIN OUTPUT ======  
../../../libs/thread/src/pthread/thread.cpp:103:58: runtime error: member call on address 0x7f69c0000b20 which does not point to an object of type 'thread_exit_function_base'  
0x7f69c0000b20: note: object is of type 'boost::detail::thread_exit_function<void (*)()>'  
 00 00 00 00  a0 9f e0 65 21 56 00 00  eb 58 dd 65 21 56 00 00  00 00 00 00 00 00 00 00  25 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::detail::thread_exit_function<void (*)()>'  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
  
    LD_LIBRARY_PATH="/boost/bin.v2/libs/chrono/build/gcc-8/debug/threading-multi/visibility-hidden:/boost/bin.v2/libs/test/build/gcc-8/debug/threading-multi/visibility-hidden:/boost/bin.v2/libs/thread/build/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden:/boost/bin.v2/libs/timer/build/gcc-8/debug/threading-multi/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit"   > "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output"  
    echo EXIT STATUS: $status >> "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output"  
    if test $status -eq 0 ; then  
        cp "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output" "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
  
...failed testing.capture-output ../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.run...  
...failed updating 1 target...  
...skipped 1 target...  
boost@1efda23852f2:/boost/libs/thread/test$ git log  
commit 5d20f5b3428560aebfd1efdd2f4c4abede11ab79 (HEAD, origin/develop, origin/HEAD)  
Author: Vicente J. Botet Escriba <vicente.botet@wanadoo.fr>  
Date:   Sun Jan 20 22:42:12 2019 +0100  
  
    Fix missing includes files and make executors available by default in version 5.  
```

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2019-02-03 17:11:48 UTC  
> Updated at: 2019-02-03 17:12:48 UTC  
> Url: https://github.com/boostorg/test/issues/188#issuecomment-460069589  

Thanks for retrying. After having a better look, it looks to me that this is a `boost.thread` problem in fact.  
  
The `0x7f423a21adf0: note: object is of type 'boost::unit_test::lazy_ostream_impl<boost::unit_test::lazy_ostream, boost::unit_test::basic_cstring<char const>, boost::unit_test::basic_cstring<char const> const&>'`  
  
seems to be fixed, isn't it?  
  
The header deprecation warning is WIP on my side

---

## Comment 7

> Username: raffienficiaud  
> Created at: 2019-07-21 19:17:29 UTC  
> Url: https://github.com/boostorg/test/issues/188#issuecomment-513580201  

Any update?

---

## Comment 8

> Username: jeking3  
> Created at: 2019-07-22 10:49:10 UTC  
> Url: https://github.com/boostorg/test/issues/188#issuecomment-513741670  

Happy to report this is fixed on develop:  
```  
boost@e923f3489224:/boost/libs/thread/test$ ubsan test_thread_exit  
+ UBSAN_OPTIONS=print_stacktrace=1  
+ b2 define=BOOST_NO_STRESS_TEST=1 undefined-sanitizer=norecover variant=debug linkflags=-fuse-ld=gold toolset=gcc-8 test_thread_exit  
Performing configuration checks  
  
    - default address-model    : 64-bit  
    - default architecture     : x86  
    - symlinks supported       : yes (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : yes  
    - lockfree boost::atomic_flag : yes  
...patience...  
...found 2965 targets...  
...updating 42 targets...  
gcc.compile.c++ ../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/undefined-sanitizer-norecover/visibility-hidden/winrt_init.o  
gcc.compile.c++ ../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/undefined-sanitizer-norecover/visibility-hidden/test_thread_exit.o  
gcc.compile.c++ ../../../bin.v2/libs/thread/build/gcc-8/debug/threadapi-pthread/threading-multi/undefined-sanitizer-norecover/visibility-hidden/pthread/once.o  
gcc.compile.c++ ../../../bin.v2/libs/thread/build/gcc-8/debug/threadapi-pthread/threading-multi/undefined-sanitizer-norecover/visibility-hidden/future.o  
gcc.compile.c++ ../../../bin.v2/libs/thread/build/gcc-8/debug/threadapi-pthread/threading-multi/undefined-sanitizer-norecover/visibility-hidden/pthread/thread.o  
gcc.link.dll ../../../bin.v2/libs/thread/build/gcc-8/debug/threadapi-pthread/threading-multi/undefined-sanitizer-norecover/visibility-hidden/libboost_thread.so.1.71.0  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/compiler_log_formatter.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/debug.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/execution_monitor.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/decorator.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/plain_report_formatter.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/progress_monitor.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/results_collector.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/results_reporter.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/test_framework_init_observer.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/framework.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/test_tools.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/test_tree.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/unit_test_log.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/unit_test_main.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/unit_test_monitor.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/junit_log_formatter.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/xml_log_formatter.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/xml_report_formatter.o  
gcc.compile.c++ ../../../bin.v2/libs/chrono/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/chrono.o  
gcc.compile.c++ ../../../bin.v2/libs/chrono/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/thread_clock.o  
gcc.compile.c++ ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/unit_test_parameters.o  
gcc.link.dll ../../../bin.v2/libs/test/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/libboost_unit_test_framework.so.1.71.0  
gcc.compile.c++ ../../../bin.v2/libs/chrono/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/process_cpu_clocks.o  
gcc.link.dll ../../../bin.v2/libs/chrono/build/gcc-8/debug/threading-multi/undefined-sanitizer-norecover/visibility-hidden/libboost_chrono.so.1.71.0  
gcc.link ../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/undefined-sanitizer-norecover/visibility-hidden/test_thread_exit  
testing.capture-output ../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/undefined-sanitizer-norecover/visibility-hidden/test_thread_exit.run  
**passed** ../../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/threadapi-pthread/threading-multi/undefined-sanitizer-norecover/visibility-hidden/test_thread_exit.test  
...updated 42 targets...  
boost@e923f3489224:/boost/libs/thread/test$  
```
