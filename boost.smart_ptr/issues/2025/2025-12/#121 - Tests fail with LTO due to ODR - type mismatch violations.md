# #121 - Tests fail with LTO due to ODR / type mismatch violations [Open]

> Username: eli-schwartz  
> Created at: 2025-12-07 21:10:22 UTC  
> Updated at: 2025-12-07 23:55:43 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/121  

I tried to build with the following *FLAGS to optimize the build: `-flto=4 -Werror=odr -Werror=lto-type-mismatch -Werror=strict-aliasing`  
  
Link-Time Optimization is a massively global compiler optimization pass which is pretty handy for producing faster executables. It also has the interesting property that because the compiler does whole-program analysis using bytecode, it can save type information and perform error checks that it normally doesn’t have enough insight for. In particular, checking for ODR issues and checking function type signature mismatches.  
  
Note that *all* the -Werror=* flags are used to help detect cases where the compiler tries to optimize by assuming UB cannot exist in the source code -- if it does exist, ordinarily the code would be miscompiled, and this says to make the miscompilation a fatal error.  
  
I got this error:  
```  
gcc.link ../../../bin.v2/libs/smart_ptr/test/abi_test_mt_nt.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/abi_test_mt_nt  
  
    "x86_64-pc-linux-gnu-g++"  -Wl,-rpath -Wl,"/var/tmp/portage/dev-libs/boost-1.89.0/work/boost_1_89_0-abi_x86_64.amd64/bin.v2/libs/atomic/build/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden" -Wl,-rpath-link -Wl,"/var/tmp/portage/dev-libs/boost-1.89.0/work/boost_1_89_0-abi_x86_64.amd64/bin.v2/libs/atomic/build/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden" -o "../../../bin.v2/libs/smart_ptr/test/abi_test_mt_nt.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/abi_test_mt_nt" -Wl,--start-group "../../../bin.v2/libs/smart_ptr/test/abi_test_mt_nt.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/abi_test_main.o" "../../../bin.v2/libs/smart_ptr/test/gcc-14.3/gentoorelease/x86_64/link-static/pch-off/threading-multi/visibility-hidden/libabi_test_nt.a" "../../../bin.v2/libs/atomic/build/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/libboost_atomic.so.1.89.0" "../../../bin.v2/libs/atomic/build/gcc-14.3/gentoorelease/x86_64/link-static/pch-off/threading-multi/visibility-hidden/libboost_atomic.a"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -fvisibility=hidden -fvisibility-inlines-hidden -Wl,-O1 -Wl,--as-needed -Wl,-z,pack-relative-relocs -flto=4 -Werror=odr -Werror=lto-type-mismatch -Werror=strict-aliasing -Wl,--defsym=__gentoo_check_ldflags__=0   
  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:70:28: error: type ‘struct sp_counted_base’ violates the C++ One Definition Rule [-Werror=odr]  
   70 | class BOOST_SYMBOL_VISIBLE sp_counted_base  
      |                            ^  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:38:28: note: a different type is defined in another translation unit  
   38 | class BOOST_SYMBOL_VISIBLE sp_counted_base  
      |                            ^  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:77:27: note: the first difference of corresponding definitions is field ‘use_count_’  
   77 |     boost::uint_least32_t use_count_;        // #shared  
      |                           ^  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:45:26: note: a field of same name but different type is defined in another translation unit  
   45 |     boost::int_least32_t use_count_;        // #shared  
      |                          ^  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:70:28: note: type ‘uint_least32_t’ should match type ‘int_least32_t’  
   70 | class BOOST_SYMBOL_VISIBLE sp_counted_base  
      |                            ^  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:86:10: error: type of ‘release’ does not match original declaration [-Werror=lto-type-mismatch]  
   86 |     void release() noexcept  
      |          ^  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:116:10: note: ‘release’ was previously declared here  
  116 |     void release() // nothrow  
      |          ^  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:116:10: note: code may be misoptimized unless ‘-fno-strict-aliasing’ is used  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:100:10: error: type of ‘weak_release’ does not match original declaration [-Werror=lto-type-mismatch]  
  100 |     void weak_release() noexcept  
      |          ^  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:130:10: note: ‘weak_release’ was previously declared here  
  130 |     void weak_release() // nothrow  
      |          ^  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:130:10: note: code may be misoptimized unless ‘-fno-strict-aliasing’ is used  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:65:18: error: type of ‘destroy’ does not match original declaration [-Werror=lto-type-mismatch]  
   65 |     virtual void destroy() noexcept // nothrow  
      |                  ^  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:97:18: note: ‘destroy’ was previously declared here  
   97 |     virtual void destroy() // nothrow  
      |                  ^  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:97:18: note: code may be misoptimized unless ‘-fno-strict-aliasing’ is used  
lto1: some warnings being treated as errors  
lto-wrapper: fatal error: /usr/bin/x86_64-pc-linux-gnu-g++ returned 1 exit status  
compilation terminated.  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: error: lto-wrapper failed  
collect2: error: ld returned 1 exit status  
...skipped <p../../../bin.v2/libs/smart_ptr/test/abi_test_mt_nt.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden>abi_test_mt_nt.run for lack of <p../../../bin.v2/libs/smart_ptr/test/abi_test_mt_nt.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden>abi_test_mt_nt...  
common.mkdir ../../../bin.v2/libs/smart_ptr/test/get_deleter_test2_no_rtti.test  
  
        mkdir -p "../../../bin.v2/libs/smart_ptr/test/get_deleter_test2_no_rtti.test"  
      
gcc.link ../../../bin.v2/libs/smart_ptr/test/abi_test_nt_mt.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/abi_test_nt_mt  
  
    "x86_64-pc-linux-gnu-g++"  -Wl,-rpath -Wl,"/var/tmp/portage/dev-libs/boost-1.89.0/work/boost_1_89_0-abi_x86_64.amd64/bin.v2/libs/atomic/build/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden" -Wl,-rpath-link -Wl,"/var/tmp/portage/dev-libs/boost-1.89.0/work/boost_1_89_0-abi_x86_64.amd64/bin.v2/libs/atomic/build/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden" -o "../../../bin.v2/libs/smart_ptr/test/abi_test_nt_mt.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/abi_test_nt_mt" -Wl,--start-group "../../../bin.v2/libs/smart_ptr/test/abi_test_nt_mt.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/abi_test_main.o" "../../../bin.v2/libs/smart_ptr/test/gcc-14.3/gentoorelease/x86_64/link-static/pch-off/threading-multi/visibility-hidden/libabi_test_mt.a" "../../../bin.v2/libs/atomic/build/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden/libboost_atomic.so.1.89.0" "../../../bin.v2/libs/atomic/build/gcc-14.3/gentoorelease/x86_64/link-static/pch-off/threading-multi/visibility-hidden/libboost_atomic.a"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -fvisibility=hidden -fvisibility-inlines-hidden -Wl,-O1 -Wl,--as-needed -Wl,-z,pack-relative-relocs -flto=4 -Werror=odr -Werror=lto-type-mismatch -Werror=strict-aliasing -Wl,--defsym=__gentoo_check_ldflags__=0   
  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:38:28: error: type ‘struct sp_counted_base’ violates the C++ One Definition Rule [-Werror=odr]  
   38 | class BOOST_SYMBOL_VISIBLE sp_counted_base  
      |                            ^  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:70:28: note: a different type is defined in another translation unit  
   70 | class BOOST_SYMBOL_VISIBLE sp_counted_base  
      |                            ^  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:45:26: note: the first difference of corresponding definitions is field ‘use_count_’  
   45 |     boost::int_least32_t use_count_;        // #shared  
      |                          ^  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:77:27: note: a field of same name but different type is defined in another translation unit  
   77 |     boost::uint_least32_t use_count_;        // #shared  
      |                           ^  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:38:28: note: type ‘int_least32_t’ should match type ‘uint_least32_t’  
   38 | class BOOST_SYMBOL_VISIBLE sp_counted_base  
      |                            ^  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:116:10: error: type of ‘release’ does not match original declaration [-Werror=lto-type-mismatch]  
  116 |     void release() // nothrow  
      |          ^  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:86:10: note: ‘release’ was previously declared here  
   86 |     void release() noexcept  
      |          ^  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:86:10: note: code may be misoptimized unless ‘-fno-strict-aliasing’ is used  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:130:10: error: type of ‘weak_release’ does not match original declaration [-Werror=lto-type-mismatch]  
  130 |     void weak_release() // nothrow  
      |          ^  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:100:10: note: ‘weak_release’ was previously declared here  
  100 |     void weak_release() noexcept  
      |          ^  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:100:10: note: code may be misoptimized unless ‘-fno-strict-aliasing’ is used  
../../../boost/smart_ptr/detail/sp_counted_base_gcc_atomic.hpp:97:18: error: type of ‘destroy’ does not match original declaration [-Werror=lto-type-mismatch]  
   97 |     virtual void destroy() // nothrow  
      |                  ^  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:65:18: note: ‘destroy’ was previously declared here  
   65 |     virtual void destroy() noexcept // nothrow  
      |                  ^  
../../../boost/smart_ptr/detail/sp_counted_base_nt.hpp:65:18: note: code may be misoptimized unless ‘-fno-strict-aliasing’ is used  
lto1: some warnings being treated as errors  
lto-wrapper: fatal error: /usr/bin/x86_64-pc-linux-gnu-g++ returned 1 exit status  
compilation terminated.  
/usr/lib/gcc/x86_64-pc-linux-gnu/14/../../../../x86_64-pc-linux-gnu/bin/ld: error: lto-wrapper failed  
collect2: error: ld returned 1 exit status  
...skipped <p../../../bin.v2/libs/smart_ptr/test/abi_test_nt_mt.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden>abi_test_nt_mt.run for lack of <p../../../bin.v2/libs/smart_ptr/test/abi_test_nt_mt.test/gcc-14.3/gentoorelease/x86_64/pch-off/threading-multi/visibility-hidden>abi_test_nt_mt...  
common.mkdir ../../../bin.v2/libs/smart_ptr/test/get_deleter_test2_no_rtti.test/gcc-14.3  
  
        mkdir -p "../../../bin.v2/libs/smart_ptr/test/get_deleter_test2_no_rtti.test/gcc-14.3"  
```  
  
Originally reported downstream: https://bugs.gentoo.org/956660  
Full build log: [build.log](https://github.com/user-attachments/files/24019656/build.log)

---

## Comment 1

> Username: pdimov  
> Created at: 2025-12-07 23:54:26 UTC  
> Updated at: 2025-12-07 23:55:43 UTC  
> Url: https://github.com/boostorg/smart_ptr/issues/121#issuecomment-3623832190  

The ODR violation in this test is deliberate, because that's what is tested - whether a program that violates the ODR in this manner (inconsistently defining BOOST_DISABLE_THREADS in some translation units and not others) still works.
