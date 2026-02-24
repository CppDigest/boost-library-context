# #415 - Tests fail with LTO due to ODR violations [Open]

> Username: eli-schwartz  
> Created at: 2025-12-07 21:12:15 UTC  
> Updated at: 2025-12-07 21:12:15 UTC  
> Url: https://github.com/boostorg/thread/issues/415  

I tried to build with the following *FLAGS to optimize the build: `-flto=4 -Werror=odr -Werror=lto-type-mismatch -Werror=strict-aliasing`  
  
Link-Time Optimization is a massively global compiler optimization pass which is pretty handy for producing faster executables. It also has the interesting property that because the compiler does whole-program analysis using bytecode, it can save type information and perform error checks that it normally doesn’t have enough insight for. In particular, checking for ODR issues and checking function type signature mismatches.  
  
Note that *all* the -Werror=* flags are used to help detect cases where the compiler tries to optimize by assuming UB cannot exist in the source code -- if it does exist, ordinarily the code would be miscompiled, and this says to make the miscompilation a fatal error.  
  
I got this error:  
```  
gcc.link ../../../bin.v2/libs/thread/test/test_10964_lib.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/test_10964_lib  
  
    "x86_64-pc-linux-gnu-g++"    -o "../../../bin.v2/libs/thread/test/test_10964_lib.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/test_10964_lib" -Wl,--start-group "../../../bin.v2/libs/thread/test/test_10964_lib.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/test_10964.o" "../../../bin.v2/libs/thread/test/test_10964_lib.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/winrt_init.o" "../../../bin.v2/libs/thread/test/test_10964_lib.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/tss_null.o" "../../../bin.v2/libs/thread/build/gcc-14.3/gentoorelease/x86_64/link-static/pch-off/threading-multi/visibility-hidden/libboost_thread.a" "../../../bin.v2/libs/exception/build/gcc-14.3/gentoorelease/x86_64/link-static/pch-off/threading-multi/visibility-hidden/libboost_exception.a" "../../../bin.v2/libs/atomic/build/gcc-14.3/gentoorelease/x86_64/link-static/pch-off/threading-multi/visibility-hidden/libboost_atomic.a" "../../../bin.v2/libs/chrono/build/gcc-14.3/gentoorelease/x86_64/link-static/pch-off/threading-multi/visibility-hidden/libboost_chrono.a" "../../../bin.v2/libs/container/build/gcc-14.3/gentoorelease/x86_64/link-static/pch-off/threading-multi/visibility-hidden/libboost_container.a" "../../../bin.v2/libs/date_time/build/gcc-14.3/gentoorelease/x86_64/link-static/pch-off/threading-multi/visibility-hidden/libboost_date_time.a"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -fvisibility=hidden -fvisibility-inlines-hidden -Wl,-O1 -Wl,--as-needed -Wl,-z,pack-relative-relocs -flto=4 -Werror=odr -Werror=lto-type-mismatch -Werror=strict-aliasing -Wl,--defsym=__gentoo_check_ldflags__=0   
  
../../../boost/thread/futures/launch.hpp:17:3: error: type ‘boost::launch’ violates the C++ One Definition Rule [-Werror=odr]  
   17 |   BOOST_SCOPED_ENUM_DECLARE_BEGIN(launch)  
      |   ^  
../../../boost/thread/futures/launch.hpp:17:3: note: an enum with different value name is defined in another translation unit  
   17 |   BOOST_SCOPED_ENUM_DECLARE_BEGIN(launch)  
      |   ^  
../../../boost/thread/futures/launch.hpp:23:7: note: name ‘executor’ differs from name ‘inherit’ defined in another translation unit  
   23 |       executor = 4,  
      |       ^  
../../../boost/thread/futures/launch.hpp:25:7: note: mismatching definition  
   25 |       inherit = 8,  
      |       ^  
../../../boost/thread/future.hpp:838:16: error: virtual table of type ‘struct shared_state’ violates one definition rule [-Werror=odr]  
  838 |         struct shared_state<void>:  
      |                ^  
../../../boost/thread/future.hpp:838:16: note: the conflicting type defined in another translation unit  
  838 |         struct shared_state<void>:  
      |                ^  
../../../boost/thread/future.hpp:327:26: note: virtual method ‘set_continuation_ptr’  
  327 |             virtual void set_continuation_ptr(continuation_ptr_type continuation, boost::unique_lock<boost::mutex>& lock)  
      |                          ^  
../../../boost/thread/future.hpp:362:26: note: ought to match virtual method ‘run_if_is_deferred’ but does not  
  362 |             virtual bool run_if_is_deferred()  
      |                          ^  
../../../boost/thread/future.hpp:151:16: error: virtual table of type ‘struct shared_state_base’ violates one definition rule [-Werror=odr]  
  151 |         struct shared_state_base : enable_shared_from_this<shared_state_base>  
      |                ^  
../../../boost/thread/future.hpp:151:16: note: the conflicting type defined in another translation unit  
  151 |         struct shared_state_base : enable_shared_from_this<shared_state_base>  
      |                ^  
../../../boost/thread/future.hpp:327:26: note: virtual method ‘set_continuation_ptr’  
  327 |             virtual void set_continuation_ptr(continuation_ptr_type continuation, boost::unique_lock<boost::mutex>& lock)  
      |                          ^  
../../../boost/thread/future.hpp:362:26: note: ought to match virtual method ‘run_if_is_deferred’ but does not  
  362 |             virtual bool run_if_is_deferred()  
      |                          ^  
lto1: some warnings being treated as errors  
lto-wrapper: fatal error: /usr/bin/x86_64-pc-linux-gnu-g++ returned 1 exit status  
compilation terminated.  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: error: lto-wrapper failed  
collect2: error: ld returned 1 exit status  
...skipped <p../../../bin.v2/libs/thread/test/test_10964_lib.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden>test_10964_lib.run for lack of <p../../../bin.v2/libs/thread/test/test_10964_lib.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden>test_10964_lib...  
gcc.link ../../../bin.v2/libs/thread/test/test_10964.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/test_10964  
```  
  
Originally reported downstream: https://bugs.gentoo.org/956660  
Full build log: [build.log](https://github.com/user-attachments/files/24019666/build.log)
