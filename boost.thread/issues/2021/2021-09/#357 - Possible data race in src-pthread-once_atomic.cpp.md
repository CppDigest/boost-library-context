# #357 - Possible data race in src/pthread/once_atomic.cpp [Open]

> Username: yuxianch  
> Created at: 2021-09-08 07:22:24 UTC  
> Updated at: 2021-09-08 09:04:16 UTC  
> Url: https://github.com/boostorg/thread/issues/357  

Possible data race is found when running test util_ipc_reliable_mq.cpp in boostorg/log. See https://github.com/boostorg/log/issues/162 for more details.  
Valgrind shows the following errors for this test:  
```  
# Run command: valgrind --tool=helgrind binary_path  
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

---

## Comment 1

> Username: Lastique  
> Created at: 2021-09-08 09:04:16 UTC  
> Url: https://github.com/boostorg/thread/issues/357#issuecomment-915054324  

The valgrind errors seem to be bogus. The first one explicitly points at two accesses to an atomic variable. The second one seem to be pointing at the TLS key access from different threads, while it is guaranteed that it is initialized first within a `call_once` before it is used in other threads.
