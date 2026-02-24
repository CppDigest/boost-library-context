# #162 - Test util_ipc_reliable_mq.cpp gets undetermized result [Closed]

> Username: yuxianch  
> Created at: 2021-09-08 03:48:50 UTC  
> Updated at: 2021-09-15 13:39:46 UTC  
> Closed at: 2021-09-15 13:39:22 UTC  
> Url: https://github.com/boostorg/log/issues/162  

When running test util_ipc_reliable_mq.cpp with other processes, sometimes it is failed in line 324 BOOST_TEST_PASSPOINT().  
https://github.com/boostorg/log/blob/d989c4778439ea1457f6ee4a9cec0a1638fe4a00/test/run/util_ipc_reliable_mq.cpp#L308-L324  
  
Latest version of clang++ is used to build the binary of the test.  
The output of the binary looks like:  
```  
util_ipc_reliable_mq: ../../../libs/log/src/posix/ipc_sync_wrappers.hpp:147: void boost::log::ipc::aux::interprocess_mutex::unlock(): Assertion `pthread_mutex_unlock(&this->mutex) == 0' failed.  
Running 4 test cases...  
unknown location(0): fatal error: in "multithreaded_message_passing": signal: SIGABRT (application abort requested)  
run/util_ipc_reliable_mq.cpp(324): last checkpoint  
```  
Could test owner please help to take a look?

---

## Comment 1

> Username: yuxianch  
> Created at: 2021-09-08 07:13:05 UTC  
> Url: https://github.com/boostorg/log/issues/162#issuecomment-914981598  

I use valgrind to detect errors in multithreads and this is what I get:  
```  
==537225== Possible data race during read of size 4 at 0x4FE76B8 by thread #3  
==537225== Locks held: none  
==537225==    at 0x4FD453C: load (core_ops_gcc_atomic.hpp:88)  
==537225==    by 0x4FD453C: load (atomic_impl.hpp:338)  
==537225==    by 0x4FD453C: boost::thread_detail::enter_once_region(boost::once_flag&) (once_atomic.cpp:39)  
==537225==    by 0x4FBFED8: void boost::call_once<void (&)()>(boost::once_flag&, void (&)()) (once_atomic.hpp:123)  
==537225==    by 0x4FBD17E: boost::detail::set_current_thread_data(boost::detail::thread_data_base*) (thread.cpp:160)  
==537225==    by 0x4FBD346: boost::(anonymous namespace)::thread_proxy(void*) (thread.cpp:174)  
==537225==    by 0x4C37EEE: mythread_wrapper (hg_intercepts.c:387)  
==537225==    by 0x5E4D2DD: start_thread (in /usr/lib64/libpthread-2.28.so)  
==537225==    by 0x6160E82: clone (in /usr/lib64/libc-2.28.so)  
==537225==  
==537225== This conflicts with a previous write of size 4 by thread #2  
==537225== Locks held: 1, at address 0x4FE77B0  
==537225==    at 0x4FD53F5: store (core_ops_gcc_atomic.hpp:76)  
==537225==    by 0x4FD53F5: store (atomic_impl.hpp:330)  
==537225==    by 0x4FD53F5: boost::thread_detail::commit_once_region(boost::once_flag&) (once_atomic.cpp:74)  
==537225==    by 0x4FBFF66: void boost::call_once<void (&)()>(boost::once_flag&, void (&)()) (once_atomic.hpp:138)  
==537225==    by 0x4FBD17E: boost::detail::set_current_thread_data(boost::detail::thread_data_base*) (thread.cpp:160)  
==537225==    by 0x4FBD346: boost::(anonymous namespace)::thread_proxy(void*) (thread.cpp:174)  
==537225==    by 0x4C37EEE: mythread_wrapper (hg_intercepts.c:387)  
==537225==    by 0x5E4D2DD: start_thread (in /usr/lib64/libpthread-2.28.so)  
==537225==    by 0x6160E82: clone (in /usr/lib64/libc-2.28.so)  
==537225==  Address 0x4fe76b8 is 0 bytes inside data symbol "_ZN5boost6detail12_GLOBAL__N_128current_thread_tls_init_flagE"  
==537225==  
==537225== ----------------------------------------------------------------  
==537225==  
==537225== Possible data race during read of size 4 at 0x4FE76BC by thread #3  
==537225== Locks held: none  
==537225==    at 0x4FBD17F: boost::detail::set_current_thread_data(boost::detail::thread_data_base*) (thread.cpp:161)  
==537225==    by 0x4FBD346: boost::(anonymous namespace)::thread_proxy(void*) (thread.cpp:174)  
==537225==    by 0x4C37EEE: mythread_wrapper (hg_intercepts.c:387)  
==537225==    by 0x5E4D2DD: start_thread (in /usr/lib64/libpthread-2.28.so)  
==537225==    by 0x6160E82: clone (in /usr/lib64/libc-2.28.so)  
==537225==  
==537225== This conflicts with a previous write of size 4 by thread #2  
==537225== Locks held: none  
==537225==    at 0x5E5476F: pthread_key_create (in /usr/lib64/libpthread-2.28.so)  
==537225==    by 0x4FC9F36: _ZN5boost6detail6invokeIPFvvEJEEEDTclclsr5boostE7forwardIT_Efp_Espclsr5boostE7forwardIT0_Efp0_EEEOS4_DpOS5_ (invoke.hpp:133)  
==537225==    by 0x4FBFF10: void boost::call_once<void (&)()>(boost::once_flag&, void (&)()) (once_atomic.hpp:127)  
==537225==    by 0x4FBD17E: boost::detail::set_current_thread_data(boost::detail::thread_data_base*) (thread.cpp:160)  
==537225==    by 0x4FBD346: boost::(anonymous namespace)::thread_proxy(void*) (thread.cpp:174)  
==537225==    by 0x4C37EEE: mythread_wrapper (hg_intercepts.c:387)  
==537225==    by 0x5E4D2DD: start_thread (in /usr/lib64/libpthread-2.28.so)  
==537225==    by 0x6160E82: clone (in /usr/lib64/libc-2.28.so)  
==537225==  Address 0x4fe76bc is 0 bytes inside data symbol "_ZN5boost6detail12_GLOBAL__N_122current_thread_tls_keyE"  
```  
Command:  
```  
valgrind --tool=helgrind binary_path  
```

---

## Comment 2

> Username: Lastique  
> Created at: 2021-09-08 09:10:28 UTC  
> Url: https://github.com/boostorg/log/issues/162#issuecomment-915060066  

The valgrind errors seem to be bogus. I commented in the Boost.Thread ticket.  
  
As for the test failure, I have never seen it happen. If you can reproduce it, please collect the backtrace of the crash. It is not possible to know what is happening from the output alone.  
  
Also, what are your Boost, system and compiler versions?

---

## Comment 3

> Username: yuxianch  
> Created at: 2021-09-10 05:41:02 UTC  
> Url: https://github.com/boostorg/log/issues/162#issuecomment-916645917  

> As for the test failure, I have never seen it happen. If you can reproduce it, please collect the backtrace of the crash. It is not possible to know what is happening from the output alone.  
  
Change BOOST_VERIFY in line 148 to assert and running the binary:  
```  
bash-4.4$ sed -n "145,149p" ./boost_1_75_0/libs/log/src/posix/ipc_sync_wrappers.hpp  
    void unlock() BOOST_NOEXCEPT  
    {  
        //BOOST_VERIFY(pthread_mutex_unlock(&this->mutex) == 0);  
        assert(pthread_mutex_unlock(&this->mutex) == 0);  
    }  
```  
The backtrace looks like:  
```  
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib64/libthread_db.so.1".  
[New Thread 0x7ffff67d6700 (LWP 1038721)]  
[New Thread 0x7ffff5fd5700 (LWP 1038722)]  
util_ipc_reliable_mq: ../../../libs/log/src/posix/ipc_sync_wrappers.hpp:148: void boost::log::ipc::aux::interprocess_mutex::unlock(): Assertion `pthread_mutex_unlock(&this->mutex) == 0' failed.  
[New Thread 0x7ffff57d4700 (LWP 1038723)]  
  
Thread 2 "util_ipc_reliab" received signal SIGABRT, Aborted.  
[Switching to Thread 0x7ffff67d6700 (LWP 1038721)]  
0x00007ffff682370f in raise () from /lib64/libc.so.6  
#0  0x00007ffff682370f in raise () from /lib64/libc.so.6  
#1  0x00007ffff680db25 in abort () from /lib64/libc.so.6  
#2  0x00007ffff680d9f9 in __assert_fail_base.cold.0 () from /lib64/libc.so.6  
#3  0x00007ffff681bcc6 in __assert_fail () from /lib64/libc.so.6  
#4  0x00007ffff7d4cca2 in boost::log::v2_mt_posix::ipc::aux::interprocess_mutex::unlock (this=0x7ffff4f53050) at ../../../libs/log/src/posix/ipc_sync_wrappers.hpp:148  
#5  0x00007ffff7d4cb98 in boost::log::v2_mt_posix::ipc::aux::interprocess_mutex::auto_unlock::~auto_unlock (this=0x7ffff67d5cc0) at ../../../libs/log/src/posix/ipc_sync_wrappers.hpp:98  
#6  0x00007ffff7d48f51 in boost::log::v2_mt_posix::ipc::reliable_message_queue::implementation::send (this=0x7ffff0000b60, message_data=0x426829, message_size=8) at ../../../libs/log/src/posix/ipc_reliable_message_queue.cpp:331  
#7  0x00007ffff7d47f6c in boost::log::v2_mt_posix::ipc::reliable_message_queue::send (this=0x7ffff67d5dc8, message_data=0x426829, message_size=8) at ../../../libs/log/src/posix/ipc_reliable_message_queue.cpp:807  
#8  0x00000000004142cd in (anonymous namespace)::multithreaded_message_passing_feeding_thread (message=0x426829 "Thread 1", failure_count=@0x7fffffff39e4: 0) at run/util_ipc_reliable_mq.cpp:300  
#9  0x0000000000421c45 in boost::_bi::list2<boost::_bi::value<char const*>, boost::reference_wrapper<unsigned int> >::operator()<void (*)(char const*, unsigned int&), boost::_bi::list0> (this=0x44ea20, f=@0x44ea18: 0x414260 <(anonymous namespace)::multithreaded_message_passing_feeding_thread(char const*, unsigned int&)>, a=...) at ../../../boost/bind/bind.hpp:297  
#10 0x0000000000421bc3 in boost::_bi::bind_t<void, void (*)(char const*, unsigned int&), boost::_bi::list2<boost::_bi::value<char const*>, boost::reference_wrapper<unsigned int> > >::operator() (this=0x44ea18) at ../../../boost/bind/bind.hpp:1272  
#11 0x000000000042148c in boost::detail::thread_data<boost::_bi::bind_t<void, void (*)(char const*, unsigned int&), boost::_bi::list2<boost::_bi::value<char const*>, boost::reference_wrapper<unsigned int> > > >::run (this=0x44e8e0) at ../../../boost/thread/detail/thread.hpp:120  
#12 0x00007ffff7c3c361 in boost::(anonymous namespace)::thread_proxy (param=0x44e8e0) at ../../../libs/thread/src/pthread/thread.cpp:179  
#13 0x00007ffff6bb62de in start_thread () from /lib64/libpthread.so.0  
#14 0x00007ffff68e7e83 in clone () from /lib64/libc.so.6  
```

---

## Comment 4

> Username: yuxianch  
> Created at: 2021-09-10 05:44:20 UTC  
> Url: https://github.com/boostorg/log/issues/162#issuecomment-916646864  

> what are your Boost, system and compiler versions?  
  
Boost 1.75 is used.  
System: redhat 8.2  
Compiler version: clang-14(latest clang++)

---

## Comment 5

> Username: Lastique  
> Created at: 2021-09-10 09:34:33 UTC  
> Url: https://github.com/boostorg/log/issues/162#issuecomment-916770148  

The latest released clang is [12.0.1](https://releases.llvm.org/download.html), I'm not sure where 14 comes from. Does it reproduce with gcc or an officially released clang?  
  
Could you show the backtrace of the other threads (i.e. the output of `thread apply all bt` in gdb)?  
  
Also, can you show what `pthread_mutex_unlock` returns? You can change the `unlock` method to something like this:  
  
```  
    void unlock() BOOST_NOEXCEPT  
    {  
        int res = pthread_mutex_unlock(&this->mutex);  
        if (res != 0)  
        {  
            char buf[1024];  
            const char* desc = strerror_r(res, buf, sizeof(buf));  
            printf("pthread_mutex_unlock returned %d: %s\n", res, desc);  
            abort();  
        }  
    }  
```

---

## Comment 6

> Username: Lastique  
> Created at: 2021-09-10 09:38:51 UTC  
> Url: https://github.com/boostorg/log/issues/162#issuecomment-916772640  

> When running test util_ipc_reliable_mq.cpp with other processes  
  
Also, what other processes? Do you mean you're running multiple instances of the same test?

---

## Comment 7

> Username: yuxianch  
> Created at: 2021-09-13 03:10:05 UTC  
> Updated at: 2021-09-13 03:10:17 UTC  
> Url: https://github.com/boostorg/log/issues/162#issuecomment-917800734  

> The latest released clang is 12.0.1, I'm not sure where 14 comes from. Does it reproduce with gcc or an officially released clang?  
  
clang-14 is clang trunk(developing version). It can be reproduced with gcc too.  
  
> what other processes? Do you mean you're running multiple instances of the same test?  
  
Whatever processes. To make the reproducing easier, I am running multiple instances of the same test.

---

## Comment 8

> Username: Lastique  
> Created at: 2021-09-13 06:49:31 UTC  
> Updated at: 2021-09-13 06:50:42 UTC  
> Url: https://github.com/boostorg/log/issues/162#issuecomment-917891303  

> Whatever processes. To make the reproducing easier, I am running multiple instances of the same test.  
  
There are always some other processes running when you run the test, and arbitrary processes don't make the test crash. Some specific ones that do specific things may affect the test, that's why I'm asking.  
  
The test creates an interprocess queue with a [fixed name](https://github.com/boostorg/log/blob/2f058c6d8c3867471d0973b6f57127b5076a4f7f/test/run/util_ipc_reliable_mq.cpp#L46), which can be opened and affected by another process. Running multiple instances of the test is one example of this - the processes will access the same IPC queue and conflict with each other, that is expected behavior and should be avoided during testing. If your other processes are using the same name for shared memory segments, they will also conflict with the test.

---

## Comment 9

> Username: Lastique  
> Created at: 2021-09-13 20:40:59 UTC  
> Url: https://github.com/boostorg/log/issues/162#issuecomment-918558948  

After updating the test to use unique IPC queue names I'm seeing errors similar to yours. I don't understand why this happens yet, but it looks like `pthread_mutex_t` in the shared memory doesn't work for some reason - I'm seeing multiple threads able to acquire the lock concurrently.

---

## Comment 10

> Username: Lastique  
> Created at: 2021-09-15 13:39:46 UTC  
> Url: https://github.com/boostorg/log/issues/162#issuecomment-920028060  

Should be fixed now. Thank you for the report.
