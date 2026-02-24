# #248 - Hang while testing Boost 1.69.0 on Fedora31 / x86_64 [Open]

> Username: trex58  
> Created at: 2020-01-16 15:29:17 UTC  
> Updated at: 2020-01-17 08:51:33 UTC  
> Url: https://github.com/boostorg/test/issues/248  

Hi,  
  
Using the official boost-1.69.0-9.fc31.spec.src.rpm and .spec file, just adding to the %check phase:  
    cd status  
    (../b2 %{?_smp_mflags} || true)  
I've got a hang while testing.  
  
I provide here-after some gdb information about what happened.  
  
The x86_64 machine has 16GB Memory, 16GB swap, 16 cores.  
The build & tests are done with -j4 .  
  
Regards,  
Hope it helps.  
  
Tony Reix  
[Uploading BuildTest.txt…]()  
[Uploading Data.txt…]()

---

## Comment 1

> Username: trex58  
> Created at: 2020-01-16 17:03:26 UTC  
> Url: https://github.com/boostorg/test/issues/248#issuecomment-575248594  

This issue is reproducible on another Fedora/x86_64 machine, which has 48 CPUs and 384GB memory. Build/tests were done with -j48 .  
The hang looks the same and at the same time during tests.

---

## Comment 2

> Username: raffienficiaud  
> Created at: 2020-01-16 19:12:21 UTC  
> Url: https://github.com/boostorg/test/issues/248#issuecomment-575300319  

Hi, the links to the log files are empty. Would you please check that? thanks!

---

## Comment 3

> Username: trex58  
> Created at: 2020-01-17 08:30:44 UTC  
> Url: https://github.com/boostorg/test/issues/248#issuecomment-575527635  

Ooops. Sorry. A file was too big. Here they are.  
  
[Data.txt](https://github.com/boostorg/test/files/4075712/Data.txt)  
[BuildTest.txt.gz](https://github.com/boostorg/test/files/4075721/BuildTest.txt.gz)  
  
The hang appeared again on the 48 cores big machine (see below) with -j1.  
However, on the 16 cores machine, with -j4, the tests ran till the end.  
  
 ├─rpmbuild -ba boost-1.69.0-9.fc31.spec  
  │   └─sh -e /var/tmp/rpm-tmp.023ZuK  
  │       └─sh -e /var/tmp/rpm-tmp.023ZuK  
  │           └─b2 -j1  
  │               └─sh -c...  
  │                   └─util_ipc_reliab  
  │                       └─3*[{util_ipc_reliab}]  
  
root      419717  419716  0 Jan16 ?        00:00:00 ../bin.v2/libs/log/test/util_ipc_reliable_mq.test/gcc-9.2.1/debug/threadapi-pthread/threading-multi/util_ipc_reliable_mq  
  
(gdb) info thread  
  Id   Target Id                                            Frame  
* 1    Thread 0x7f6443cbd740 (LWP 419717) "util_ipc_reliab" 0x00007f6443ff8d45 in pthread_cond_wait@@GLIBC_2.3.2 () from /usr/lib64/libpthread.so.0  
  2    Thread 0x7f6443cb9700 (LWP 419718) "util_ipc_reliab" 0x00007f6443ff8d45 in pthread_cond_wait@@GLIBC_2.3.2 () from /usr/lib64/libpthread.so.0  
  3    Thread 0x7f64434b8700 (LWP 419719) "util_ipc_reliab" 0x00007f6443ff8d45 in pthread_cond_wait@@GLIBC_2.3.2 () from /usr/lib64/libpthread.so.0  
  4    Thread 0x7f643acb7700 (LWP 419720) "util_ipc_reliab" 0x00007f6443ff8d45 in pthread_cond_wait@@GLIBC_2.3.2 () from /usr/lib64/libpthread.so.0

---

## Comment 4

> Username: trex58  
> Created at: 2020-01-17 08:51:31 UTC  
> Url: https://github.com/boostorg/test/issues/248#issuecomment-575533911  

I saw the same hang on Fedora/PPC64LE :   
  
====== END OUTPUT ======  
.........  
...failed testing.capture-output ../bin.v2/libs/process/test/environment.test/gcc-9.2.1/debug/visibility-hidden/environment.run...  
gcc.compile.c++ ../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.o  
gcc.link ../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe  
testing.capture-output ../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/**async_pipe.run**  
  
  
**Details:**  
  
# ps -edf | grep 2486735  
  root     2486735 1289775  0 déc.04 pts/7  00:01:56 ../b2  
  root     2594484 2486735  0 03:18 pts/7    00:00:00 /bin/sh -c      LD_LIBRARY_PATH="/root/rpmbuild/BUILD/boost_1_69_0/bin.v2/libs/filesystem/build/gcc-9.2.1/debug/visibility-hidden:/root/rpmbuild/BUILD/boost_1_69_0/bin.v2/libs/system/build/gcc-9.2.1/debug/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH" export LD_LIBRARY_PATH      status=0     if test $status -ne 0 ; then         echo Skipping test execution due to testing.execute=off         exit 0     fi      "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe"   > "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.output" 2>&1 < /dev/null     status=$?     echo >> "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.output"     echo EXIT STATUS: $status >> "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.output"     if test $status -eq 0 ; then         cp "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.output" "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.run"     fi     verbose=0     if test $status -ne 0 ; then         verbose=1     fi     if test $verbose -eq 1 ; then         echo ====== BEGIN OUTPUT ======         cat "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.output"         echo ====== END OUTPUT ======     fi     exit $status   
    
  # ps -edf | grep 2594484  
  root     2594484 2486735  0 03:18 pts/7    00:00:00 /bin/sh -c      LD_LIBRARY_PATH="/root/rpmbuild/BUILD/boost_1_69_0/bin.v2/libs/filesystem/build/gcc-9.2.1/debug/visibility-hidden:/root/rpmbuild/BUILD/boost_1_69_0/bin.v2/libs/system/build/gcc-9.2.1/debug/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH" export LD_LIBRARY_PATH      status=0     if test $status -ne 0 ; then         echo Skipping test execution due to testing.execute=off         exit 0     fi      "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe"   > "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.output" 2>&1 < /dev/null     status=$?     echo >> "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.output"     echo EXIT STATUS: $status >> "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.output"     if test $status -eq 0 ; then         cp "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.output" "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.run"     fi     verbose=0     if test $status -ne 0 ; then         verbose=1     fi     if test $verbose -eq 1 ; then         echo ====== BEGIN OUTPUT ======         cat "../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe.output"         echo ====== END OUTPUT ======     fi     exit $status   
  root     2594485 2594484  0 03:18 pts/7    00:00:00 ../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe  
    
    
  # ps -edf | grep 2594485  
  root     2594485 2594484  0 03:18 pts/7    00:00:00 ../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe  
  root     2603242 1289707  0 10:58 pts/0    00:00:00 grep --color=auto 2594485  
  [root@castordbe SPECS]# pstree 2486735  
  b2───sh───async_pipe───19*[{async_pipe}]  
    
  # ps -edf | grep 2594485  
  root     2594485 2594484  0 03:18 pts/7    00:00:00 ../bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe  
  root     2603251 1289707  0 10:58 pts/0    00:00:00 grep --color=auto 2594485  
    
    
    
  # cd $BUILD  
  # cd boost_1_69_0/  
  # ll ./bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe  
  -rwxr-xr-x 1 root root 7741720  5 déc.  03:18 ./bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe  
  # file ./bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe  
  ./bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe: ELF 64-bit LSB executable, 64-bit PowerPC or cisco 7500, version 1 (GNU/Linux), dynamically linked, interpreter /lib64/ld64.so.2, BuildID[sha1]=349899f6052dec92ad3db92a17ee7cd37d8ebb47, for GNU/Linux 3.10.0, with debug_info, not stripped  
    
    
  # gdb ./bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe  
  GNU gdb (GDB) Fedora 8.3.50.20190824-25.fc31  
  Copyright (C) 2019 Free Software Foundation, Inc.  
  License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
  This is free software: you are free to change and redistribute it.  
  There is NO WARRANTY, to the extent permitted by law.  
  Type "show copying" and "show warranty" for details.  
  This GDB was configured as "ppc64le-redhat-linux-gnu".  
  Type "show configuration" for configuration details.  
  For bug reporting instructions, please see:  
  <http://www.gnu.org/software/gdb/bugs/>.  
  Find the GDB manual and other documentation resources online at:  
      <http://www.gnu.org/software/gdb/documentation/>.  
    
  For help, type "help".  
  Type "apropos word" to search for commands related to "word"...  
  Reading symbols from ./bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe...  
  (gdb) attach 2594485  
  Attaching to program: /root/rpmbuild/BUILD/boost_1_69_0/bin.v2/libs/process/test/async_pipe.test/gcc-9.2.1/debug/visibility-hidden/async_pipe, process 2594485  
  [New LWP 2594487]  
  [New LWP 2594488]  
  [New LWP 2594489]  
  [New LWP 2594490]  
  [New LWP 2594491]  
  [New LWP 2594493]  
  [New LWP 2594495]  
  [New LWP 2594497]  
  [New LWP 2594500]  
  [New LWP 2594503]  
  [New LWP 2594506]  
  [New LWP 2594507]  
  [New LWP 2594508]  
  [New LWP 2594510]  
  [New LWP 2594511]  
  [New LWP 2594513]  
  [New LWP 2594514]  
  [New LWP 2594515]  
  [New LWP 2594517]  
  [Thread debugging using libthread_db enabled]  
  Using host libthread_db library "/lib64/libthread_db.so.1".  
  0x00007fff94bbb3c4 in __pthread_timedjoin_ex () from /usr/lib64/libpthread.so.0  
  Missing separate debuginfos, use: dnf debuginfo-install glibc-2.30-7.fc31.ppc64le libgcc-9.2.1-1.fc31.ppc64le libstdc++-9.2.1-1.fc31.ppc64le  
  (gdb) i thread  
    Id   Target Id                                        Frame   
  * 1    Thread 0x7fff94cf5c30 (LWP 2594485) "async_pipe" 0x00007fff94bbb3c4 in __pthread_timedjoin_ex () from /usr/lib64/libpthread.so.0  
    2    Thread 0x7fff93b7f160 (LWP 2594487) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    3    Thread 0x7fff8bfff160 (LWP 2594488) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    4    Thread 0x7fff9316f160 (LWP 2594489) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    5    Thread 0x7fff9275f160 (LWP 2594490) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    6    Thread 0x7fff91d4f160 (LWP 2594491) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    7    Thread 0x7fff8b5ef160 (LWP 2594493) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    8    Thread 0x7fff8a1cf160 (LWP 2594495) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    9    Thread 0x7fff88daf160 (LWP 2594497) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    10   Thread 0x7fff62bdf160 (LWP 2594500) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    11   Thread 0x7fff60daf160 (LWP 2594503) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    12   Thread 0x7fff46bdf160 (LWP 2594506) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    13   Thread 0x7fff461cf160 (LWP 2594507) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    14   Thread 0x7fff457bf160 (LWP 2594508) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    15   Thread 0x7fff2bfff160 (LWP 2594510) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    16   Thread 0x7fff2b5ef160 (LWP 2594511) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    17   Thread 0x7fff2a1cf160 (LWP 2594513) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    18   Thread 0x7fff297bf160 (LWP 2594514) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    19   Thread 0x7fff28daf160 (LWP 2594515) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
    20   Thread 0x7fff0f5ef160 (LWP 2594517) "async_pipe" 0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
  (gdb) where  
  #0  0x00007fff94bbb3c4 in __pthread_timedjoin_ex () from /usr/lib64/libpthread.so.0  
  #1  0x0000000000000000 in ?? ()  
  (gdb) thread 2  
  [Switching to thread 2 (Thread 0x7fff93b7f160 (LWP 2594487))]  
  #0  0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
  (gdb) where  
  #0  0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
  #1  0x00007fff94bbd3cc in pthread_mutex_lock () from /usr/lib64/libpthread.so.0  
  #2  0x00007fff947164c4 in dl_iterate_phdr () from /usr/lib64/libc.so.6  
  #3  0x00007fff947af1f8 in _Unwind_Find_FDE () from /usr/lib64/libgcc_s.so.1  
  #4  0x00007fff947a9d7c in uw_frame_state_for () from /usr/lib64/libgcc_s.so.1  
  #5  0x00007fff947ac088 in _Unwind_RaiseException () from /usr/lib64/libgcc_s.so.1  
  #6  0x00007fff949cf27c in __cxa_throw () from /usr/lib64/libstdc++.so.6  
  #7  0x00000000100a5314 in boost::throw_exception<boost::system::system_error> (e=...) at ../boost/throw_exception.hpp:70  
  #8  0x000000001007d5cc in boost::asio::detail::do_throw_error (err=..., location=0x101383a0 "assign") at ../boost/asio/detail/impl/throw_error.ipp:38  
  #9  0x000000001007d4d0 in boost::asio::detail::throw_error (err=..., location=0x101383a0 "assign") at ../boost/asio/detail/throw_error.hpp:42  
  #10 0x000000001008624c in boost::asio::posix::descriptor::assign (this=0x7fff84009a68, native_descriptor=@0x7fff93b7e4d4: 113) at ../boost/asio/posix/descriptor.hpp:219  
  #11 0x0000000010086678 in boost::process::detail::posix::async_pipe::async_pipe (this=0x7fff84009a50, ios_source=..., ios_sink=...)  
      at ../boost/process/detail/posix/async_pipe.hpp:36  
  #12 0x0000000010086538 in boost::process::detail::posix::async_pipe::async_pipe (this=0x7fff84009a50, ios=...) at ../boost/process/detail/posix/async_pipe.hpp:26  
  #13 0x000000001003c708 in multithreaded_async_pipe::<lambda()>::operator()(void) const (__closure=0x1003f1db778) at ../libs/process/test/async_pipe.cpp:78  
  #14 0x000000001005b170 in std::__invoke_impl<void, multithreaded_async_pipe::test_method()::<lambda()> >(std::__invoke_other, multithreaded_async_pipe::<lambda()> &&) (__f=...)  
      at /usr/include/c++/9/bits/invoke.h:60  
  #15 0x000000001005b08c in std::__invoke<multithreaded_async_pipe::test_method()::<lambda()> >(multithreaded_async_pipe::<lambda()> &&) (__fn=...)  
      at /usr/include/c++/9/bits/invoke.h:95  
  #16 0x000000001005afa0 in std::thread::_Invoker<std::tuple<multithreaded_async_pipe::test_method()::<lambda()> > >::_M_invoke<0>(std::_Index_tuple<0>) (this=0x1003f1db778)  
      at /usr/include/c++/9/thread:244  
  #17 0x000000001005af38 in std::thread::_Invoker<std::tuple<multithreaded_async_pipe::test_method()::<lambda()> > >::operator()(void) (this=0x1003f1db778)  
      at /usr/include/c++/9/thread:251  
  #18 0x000000001005aee8 in std::thread::_State_impl<std::thread::_Invoker<std::tuple<multithreaded_async_pipe::test_method()::<lambda()> > > >::_M_run(void) (this=0x1003f1db770)  
      at /usr/include/c++/9/thread:195  
  #19 0x00007fff94a14270 in execute_native_thread_routine () from /usr/lib64/libstdc++.so.6  
  #20 0x00007fff94bb9a88 in start_thread () from /usr/lib64/libpthread.so.0  
  #21 0x00007fff946c3ad8 in clone () from /usr/lib64/libc.so.6  
  (gdb) thread 3  
  [Switching to thread 3 (Thread 0x7fff8bfff160 (LWP 2594488))]  
  #0  0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
  (gdb) where  
  #0  0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
  #1  0x00007fff94bbd3cc in pthread_mutex_lock () from /usr/lib64/libpthread.so.0  
  #2  0x00007fff947164c4 in dl_iterate_phdr () from /usr/lib64/libc.so.6  
  #3  0x00007fff947af1f8 in _Unwind_Find_FDE () from /usr/lib64/libgcc_s.so.1  
  #4  0x00007fff947a9d7c in uw_frame_state_for () from /usr/lib64/libgcc_s.so.1  
  #5  0x00007fff947ac088 in _Unwind_RaiseException () from /usr/lib64/libgcc_s.so.1  
  #6  0x00007fff949cf27c in __cxa_throw () from /usr/lib64/libstdc++.so.6  
  #7  0x00000000100a5314 in boost::throw_exception<boost::system::system_error> (e=...) at ../boost/throw_exception.hpp:70  
  #8  0x000000001007d5cc in boost::asio::detail::do_throw_error (err=..., location=0x101383a0 "assign") at ../boost/asio/detail/impl/throw_error.ipp:38  
  #9  0x000000001007d4d0 in boost::asio::detail::throw_error (err=..., location=0x101383a0 "assign") at ../boost/asio/detail/throw_error.hpp:42  
  #10 0x000000001008624c in boost::asio::posix::descriptor::assign (this=0x7fff7c0082d8, native_descriptor=@0x7fff8bffe4d4: 289) at ../boost/asio/posix/descriptor.hpp:219  
  #11 0x0000000010086678 in boost::process::detail::posix::async_pipe::async_pipe (this=0x7fff7c0082c0, ios_source=..., ios_sink=...)  
      at ../boost/process/detail/posix/async_pipe.hpp:36  
  #12 0x0000000010086538 in boost::process::detail::posix::async_pipe::async_pipe (this=0x7fff7c0082c0, ios=...) at ../boost/process/detail/posix/async_pipe.hpp:26  
  #13 0x000000001003c708 in multithreaded_async_pipe::<lambda()>::operator()(void) const (__closure=0x1003f1d75d8) at ../libs/process/test/async_pipe.cpp:78  
  #14 0x000000001005b170 in std::__invoke_impl<void, multithreaded_async_pipe::test_method()::<lambda()> >(std::__invoke_other, multithreaded_async_pipe::<lambda()> &&) (__f=...)  
      at /usr/include/c++/9/bits/invoke.h:60  
  #15 0x000000001005b08c in std::__invoke<multithreaded_async_pipe::test_method()::<lambda()> >(multithreaded_async_pipe::<lambda()> &&) (__fn=...)  
      at /usr/include/c++/9/bits/invoke.h:95  
  #16 0x000000001005afa0 in std::thread::_Invoker<std::tuple<multithreaded_async_pipe::test_method()::<lambda()> > >::_M_invoke<0>(std::_Index_tuple<0>) (this=0x1003f1d75d8)  
      at /usr/include/c++/9/thread:244  
  #17 0x000000001005af38 in std::thread::_Invoker<std::tuple<multithreaded_async_pipe::test_method()::<lambda()> > >::operator()(void) (this=0x1003f1d75d8)  
      at /usr/include/c++/9/thread:251  
  #18 0x000000001005aee8 in std::thread::_State_impl<std::thread::_Invoker<std::tuple<multithreaded_async_pipe::test_method()::<lambda()> > > >::_M_run(void) (this=0x1003f1d75d0)  
      at /usr/include/c++/9/thread:195  
  #19 0x00007fff94a14270 in execute_native_thread_routine () from /usr/lib64/libstdc++.so.6  
  #20 0x00007fff94bb9a88 in start_thread () from /usr/lib64/libpthread.so.0  
  #21 0x00007fff946c3ad8 in clone () from /usr/lib64/libc.so.6  
  (gdb)   
  #0  0x00007fff94bc8fb8 in __lll_lock_wait () from /usr/lib64/libpthread.so.0  
  #1  0x00007fff94bbd3cc in pthread_mutex_lock () from /usr/lib64/libpthread.so.0  
  #2  0x00007fff947164c4 in dl_iterate_phdr () from /usr/lib64/libc.so.6  
  #3  0x00007fff947af1f8 in _Unwind_Find_FDE () from /usr/lib64/libgcc_s.so.1  
  #4  0x00007fff947a9d7c in uw_frame_state_for () from /usr/lib64/libgcc_s.so.1  
  #5  0x00007fff947ac088 in _Unwind_RaiseException () from /usr/lib64/libgcc_s.so.1  
  #6  0x00007fff949cf27c in __cxa_throw () from /usr/lib64/libstdc++.so.6  
  #7  0x00000000100a5314 in boost::throw_exception<boost::system::system_error> (e=...) at ../boost/throw_exception.hpp:70  
  #8  0x000000001007d5cc in boost::asio::detail::do_throw_error (err=..., location=0x101383a0 "assign") at ../boost/asio/detail/impl/throw_error.ipp:38  
  #9  0x000000001007d4d0 in boost::asio::detail::throw_error (err=..., location=0x101383a0 "assign") at ../boost/asio/detail/throw_error.hpp:42  
  #10 0x000000001008624c in boost::asio::posix::descriptor::assign (this=0x7fff7c0082d8, native_descriptor=@0x7fff8bffe4d4: 289) at ../boost/asio/posix/descriptor.hpp:219  
  #11 0x0000000010086678 in boost::process::detail::posix::async_pipe::async_pipe (this=0x7fff7c0082c0, ios_source=..., ios_sink=...)  
      at ../boost/process/detail/posix/async_pipe.hpp:36  
  #12 0x0000000010086538 in boost::process::detail::posix::async_pipe::async_pipe (this=0x7fff7c0082c0, ios=...) at ../boost/process/detail/posix/async_pipe.hpp:26  
  #13 0x000000001003c708 in multithreaded_async_pipe::<lambda()>::operator()(void) const (__closure=0x1003f1d75d8) at ../libs/process/test/async_pipe.cpp:78  
  #14 0x000000001005b170 in std::__invoke_impl<void, multithreaded_async_pipe::test_method()::<lambda()> >(std::__invoke_other, multithreaded_async_pipe::<lambda()> &&) (__f=...)  
      at /usr/include/c++/9/bits/invoke.h:60  
  #15 0x000000001005b08c in std::__invoke<multithreaded_async_pipe::test_method()::<lambda()> >(multithreaded_async_pipe::<lambda()> &&) (__fn=...)  
      at /usr/include/c++/9/bits/invoke.h:95  
  #16 0x000000001005afa0 in std::thread::_Invoker<std::tuple<multithreaded_async_pipe::test_method()::<lambda()> > >::_M_invoke<0>(std::_Index_tuple<0>) (this=0x1003f1d75d8)  
      at /usr/include/c++/9/thread:244  
  #17 0x000000001005af38 in std::thread::_Invoker<std::tuple<multithreaded_async_pipe::test_method()::<lambda()> > >::operator()(void) (this=0x1003f1d75d8)  
      at /usr/include/c++/9/thread:251  
  #18 0x000000001005aee8 in std::thread::_State_impl<std::thread::_Invoker<std::tuple<multithreaded_async_pipe::test_method()::<lambda()> > > >::_M_run(void) (this=0x1003f1d75d0)  
      at /usr/include/c++/9/thread:195  
  #19 0x00007fff94a14270 in execute_native_thread_routine () from /usr/lib64/libstdc++.so.6  
  #20 0x00007fff94bb9a88 in start_thread () from /usr/lib64/libpthread.so.0  
  #21 0x00007fff946c3ad8 in clone () from /usr/lib64/libc.so.6
