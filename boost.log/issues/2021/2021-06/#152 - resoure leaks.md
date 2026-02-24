# #152 - resoure leaks [Closed]

> Username: nns52k  
> Created at: 2021-06-17 06:19:40 UTC  
> Updated at: 2021-06-17 08:42:05 UTC  
> Closed at: 2021-06-17 08:42:05 UTC  
> Url: https://github.com/boostorg/log/issues/152  

My compiler is GNU C++ 8.3.0  
My operating system is Debian 10  
  
Running this example  
https://www.boost.org/doc/libs/1_76_0/libs/log/example/doc/tutorial_logging.cpp  
would lead to memory leaks.  
  
The report from Valgrind is  
```  
==8220== Memcheck, a memory error detector  
==8220== Copyright (C) 2002-2017, and GNU GPL'd, by Julian Seward et al.  
==8220== Using Valgrind-3.14.0 and LibVEX; rerun with -h for copyright info  
==8220== Command: ./a.out  
==8220== Parent PID: 10913  
==8220==   
==8220== Mismatched free() / delete / delete []  
==8220==    at 0x48369AB: free (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)  
==8220==    by 0x6EF1629: check_free (dlerror.c:202)  
==8220==    by 0x6EF1629: check_free (dlerror.c:186)  
==8220==    by 0x6EF1AB1: free_key_mem (dlerror.c:221)  
==8220==    by 0x6EF1AB1: __dlerror_main_freeres (dlerror.c:239)  
==8220==    by 0x4E12B71: __libc_freeres (in /usr/lib/x86_64-linux-gnu/libc-2.28.so)  
==8220==    by 0x482B19E: _vgnU_freeres (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_core-amd64-linux.so)  
==8220==    by 0x4CE3E89: __run_exit_handlers (exit.c:132)  
==8220==    by 0x4CE3EB9: exit (exit.c:139)  
==8220==    by 0x4CCE0A1: (below main) (libc-start.c:342)  
==8220==  Address 0x6f12d00 is 0 bytes inside a block of size 312 alloc'd  
==8220==    at 0x4835DEF: operator new(unsigned long) (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)  
==8220==    by 0x495472A: ??? (in /usr/local/lib/libboost_thread.so.1.76.0)  
==8220==    by 0x49558B4: ??? (in /usr/local/lib/libboost_thread.so.1.76.0)  
==8220==    by 0x48C2A00: boost::log::v2_mt_posix::core::open_record(boost::log::v2_mt_posix::attribute_set const&) (in /usr/local/lib/libboost_log.so.1.76.0)  
==8220==    by 0x118639: boost::log::v2_mt_posix::record boost::log::v2_mt_posix::sources::basic_logger<char, boost::log::v2_mt_posix::sources::logger, boost::log::v2_mt_posix::sources::single_thread_model>::open_record_unlocked<boost::parameter::aux::empty_arg_list>(boost::parameter::aux::empty_arg_list const&) (basic_logger.hpp:260)  
==8220==    by 0x117385: boost::log::v2_mt_posix::sources::basic_composite_logger<char, boost::log::v2_mt_posix::sources::logger, boost::log::v2_mt_posix::sources::single_thread_model, boost::log::v2_mt_posix::sources::features<> >::open_record() (basic_logger.hpp:567)  
==8220==    by 0x1119A5: logging_function1() (tutorial_logging.cpp:26)  
==8220==    by 0x111B70: main (tutorial_logging.cpp:48)  
==8220==   
==8220== Invalid write of size 8  
==8220==    at 0x6EF162A: check_free (dlerror.c:203)  
==8220==    by 0x6EF162A: check_free (dlerror.c:186)  
==8220==    by 0x6EF1AB1: free_key_mem (dlerror.c:221)  
==8220==    by 0x6EF1AB1: __dlerror_main_freeres (dlerror.c:239)  
==8220==    by 0x4E12B71: __libc_freeres (in /usr/lib/x86_64-linux-gnu/libc-2.28.so)  
==8220==    by 0x482B19E: _vgnU_freeres (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_core-amd64-linux.so)  
==8220==    by 0x4CE3E89: __run_exit_handlers (exit.c:132)  
==8220==    by 0x4CE3EB9: exit (exit.c:139)  
==8220==    by 0x4CCE0A1: (below main) (libc-start.c:342)  
==8220==  Address 0x6f12d18 is 24 bytes inside a block of size 312 free'd  
==8220==    at 0x48369AB: free (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)  
==8220==    by 0x6EF1629: check_free (dlerror.c:202)  
==8220==    by 0x6EF1629: check_free (dlerror.c:186)  
==8220==    by 0x6EF1AB1: free_key_mem (dlerror.c:221)  
==8220==    by 0x6EF1AB1: __dlerror_main_freeres (dlerror.c:239)  
==8220==    by 0x4E12B71: __libc_freeres (in /usr/lib/x86_64-linux-gnu/libc-2.28.so)  
==8220==    by 0x482B19E: _vgnU_freeres (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_core-amd64-linux.so)  
==8220==    by 0x4CE3E89: __run_exit_handlers (exit.c:132)  
==8220==    by 0x4CE3EB9: exit (exit.c:139)  
==8220==    by 0x4CCE0A1: (below main) (libc-start.c:342)  
==8220==  Block was alloc'd at  
==8220==    at 0x4835DEF: operator new(unsigned long) (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)  
==8220==    by 0x495472A: ??? (in /usr/local/lib/libboost_thread.so.1.76.0)  
==8220==    by 0x49558B4: ??? (in /usr/local/lib/libboost_thread.so.1.76.0)  
==8220==    by 0x48C2A00: boost::log::v2_mt_posix::core::open_record(boost::log::v2_mt_posix::attribute_set const&) (in /usr/local/lib/libboost_log.so.1.76.0)  
==8220==    by 0x118639: boost::log::v2_mt_posix::record boost::log::v2_mt_posix::sources::basic_logger<char, boost::log::v2_mt_posix::sources::logger, boost::log::v2_mt_posix::sources::single_thread_model>::open_record_unlocked<boost::parameter::aux::empty_arg_list>(boost::parameter::aux::empty_arg_list const&) (basic_logger.hpp:260)  
==8220==    by 0x117385: boost::log::v2_mt_posix::sources::basic_composite_logger<char, boost::log::v2_mt_posix::sources::logger, boost::log::v2_mt_posix::sources::single_thread_model, boost::log::v2_mt_posix::sources::features<> >::open_record() (basic_logger.hpp:567)  
==8220==    by 0x1119A5: logging_function1() (tutorial_logging.cpp:26)  
==8220==    by 0x111B70: main (tutorial_logging.cpp:48)  
==8220==   
==8220== Invalid free() / delete / delete[] / realloc()  
==8220==    at 0x48369AB: free (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)  
==8220==    by 0x6EF1AB9: free_key_mem (dlerror.c:223)  
==8220==    by 0x6EF1AB9: __dlerror_main_freeres (dlerror.c:239)  
==8220==    by 0x4E12B71: __libc_freeres (in /usr/lib/x86_64-linux-gnu/libc-2.28.so)  
==8220==    by 0x482B19E: _vgnU_freeres (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_core-amd64-linux.so)  
==8220==    by 0x4CE3E89: __run_exit_handlers (exit.c:132)  
==8220==    by 0x4CE3EB9: exit (exit.c:139)  
==8220==    by 0x4CCE0A1: (below main) (libc-start.c:342)  
==8220==  Address 0x6f12d00 is 0 bytes inside a block of size 312 free'd  
==8220==    at 0x48369AB: free (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)  
==8220==    by 0x6EF1629: check_free (dlerror.c:202)  
==8220==    by 0x6EF1629: check_free (dlerror.c:186)  
==8220==    by 0x6EF1AB1: free_key_mem (dlerror.c:221)  
==8220==    by 0x6EF1AB1: __dlerror_main_freeres (dlerror.c:239)  
==8220==    by 0x4E12B71: __libc_freeres (in /usr/lib/x86_64-linux-gnu/libc-2.28.so)  
==8220==    by 0x482B19E: _vgnU_freeres (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_core-amd64-linux.so)  
==8220==    by 0x4CE3E89: __run_exit_handlers (exit.c:132)  
==8220==    by 0x4CE3EB9: exit (exit.c:139)  
==8220==    by 0x4CCE0A1: (below main) (libc-start.c:342)  
==8220==  Block was alloc'd at  
==8220==    at 0x4835DEF: operator new(unsigned long) (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)  
==8220==    by 0x495472A: ??? (in /usr/local/lib/libboost_thread.so.1.76.0)  
==8220==    by 0x49558B4: ??? (in /usr/local/lib/libboost_thread.so.1.76.0)  
==8220==    by 0x48C2A00: boost::log::v2_mt_posix::core::open_record(boost::log::v2_mt_posix::attribute_set const&) (in /usr/local/lib/libboost_log.so.1.76.0)  
==8220==    by 0x118639: boost::log::v2_mt_posix::record boost::log::v2_mt_posix::sources::basic_logger<char, boost::log::v2_mt_posix::sources::logger, boost::log::v2_mt_posix::sources::single_thread_model>::open_record_unlocked<boost::parameter::aux::empty_arg_list>(boost::parameter::aux::empty_arg_list const&) (basic_logger.hpp:260)  
==8220==    by 0x117385: boost::log::v2_mt_posix::sources::basic_composite_logger<char, boost::log::v2_mt_posix::sources::logger, boost::log::v2_mt_posix::sources::single_thread_model, boost::log::v2_mt_posix::sources::features<> >::open_record() (basic_logger.hpp:567)  
==8220==    by 0x1119A5: logging_function1() (tutorial_logging.cpp:26)  
==8220==    by 0x111B70: main (tutorial_logging.cpp:48)  
==8220==   
==8220==   
==8220== HEAP SUMMARY:  
==8220==     in use at exit: 32 bytes in 2 blocks  
==8220==   total heap usage: 81 allocs, 80 frees, 94,737 bytes allocated  
==8220==   
==8220== 24 bytes in 1 blocks are definitely lost in loss record 2 of 2  
==8220==    at 0x4835DEF: operator new(unsigned long) (in /usr/lib/x86_64-linux-gnu/valgrind/vgpreload_memcheck-amd64-linux.so)  
==8220==    by 0x49548DC: ??? (in /usr/local/lib/libboost_thread.so.1.76.0)  
==8220==    by 0x49558B4: ??? (in /usr/local/lib/libboost_thread.so.1.76.0)  
==8220==    by 0x48C2A00: boost::log::v2_mt_posix::core::open_record(boost::log::v2_mt_posix::attribute_set const&) (in /usr/local/lib/libboost_log.so.1.76.0)  
==8220==    by 0x118639: boost::log::v2_mt_posix::record boost::log::v2_mt_posix::sources::basic_logger<char, boost::log::v2_mt_posix::sources::logger, boost::log::v2_mt_posix::sources::single_thread_model>::open_record_unlocked<boost::parameter::aux::empty_arg_list>(boost::parameter::aux::empty_arg_list const&) (basic_logger.hpp:260)  
==8220==    by 0x117385: boost::log::v2_mt_posix::sources::basic_composite_logger<char, boost::log::v2_mt_posix::sources::logger, boost::log::v2_mt_posix::sources::single_thread_model, boost::log::v2_mt_posix::sources::features<> >::open_record() (basic_logger.hpp:567)  
==8220==    by 0x1119A5: logging_function1() (tutorial_logging.cpp:26)  
==8220==    by 0x111B70: main (tutorial_logging.cpp:48)  
==8220==   
==8220== LEAK SUMMARY:  
==8220==    definitely lost: 24 bytes in 1 blocks  
==8220==    indirectly lost: 0 bytes in 0 blocks  
==8220==      possibly lost: 0 bytes in 0 blocks  
==8220==    still reachable: 8 bytes in 1 blocks  
==8220==         suppressed: 0 bytes in 0 blocks  
==8220== Reachable blocks (those to which a pointer was found) are not shown.  
==8220== To see them, rerun with: --leak-check=full --show-leak-kinds=all  
==8220==   
==8220== For counts of detected and suppressed errors, rerun with: -v  
==8220== ERROR SUMMARY: 4 errors from 4 contexts (suppressed: 0 from 0)  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2021-06-17 08:42:05 UTC  
> Url: https://github.com/boostorg/log/issues/152#issuecomment-863051428  

As can be seen from the stack traces, the allocations are made in Boost.Thread. Please, reproduce with its debug symbols installed and report the issue [there](https://github.com/boostorg/thread/issues).
