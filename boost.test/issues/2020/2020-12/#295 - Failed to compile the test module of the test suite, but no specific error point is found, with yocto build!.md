# #295 - Failed to compile the test module of the test suite, but no specific error point is found, with yocto build! [Open]

> Username: linjx123456  
> Created at: 2020-12-29 07:11:41 UTC  
> Updated at: 2022-02-27 01:24:43 UTC  
> Url: https://github.com/boostorg/test/issues/295  

```  
| SEM: <s>gcc-link-semaphore is busy, delaying launch of <pYYY/boost/bin.v2/libs/test/test/single-header-custom-init-test.test/gcc-gnu-4.3.1/debug/threadapi-pthread>single-header-custom-init-test  
| 0.544040 sec system; 7.765044 sec user; 8.000000 sec clock  
| SEM: <s>gcc-link-semaphore is busy, delaying launch of <pYYY/boost/bin.v2/libs/test/test/test_unit-order-shuffled-test.test/gcc-gnu-4.3.1/debug/threadapi-pthread>test_unit-order-shuffled-test  
| 0.533589 sec system; 7.637313 sec user; 7.000000 sec clock  
| SEM: <s>gcc-link-semaphore is busy, delaying launch of <pYYY/boost/bin.v2/libs/test/test/gcc-gnu-4.3.1/debug/threadapi-pthread>smoke-ts-included-2  
| 0.477755 sec system; 7.969091 sec user; 8.000000 sec clock  
| SEM: <s>gcc-link-semaphore is busy, delaying launch of <pYYY/boost/bin.v2/libs/test/test/gcc-gnu-4.3.1/debug/threadapi-pthread>smoke-ts-included  
| WARNING: YYY/temp/run.do_compile.15739:1 exit 1 from 'bjam -j32 -d+4 -q --ignore-site-config '-sTOOLS=gcc' '-sGCC=aarch64-linux-gnu-gcc  -march=armv8-a+crc -fstack-protector-strong -pie -fPIE -D_FORTIFY_SOURCE=2 -Wformat -Wformat-security -Werror=format-security --sysroot=YYY/recipe-sysroot '"'-DBOOST_PLATFORM_CONFIG=\"boost/config/platform/linux.hpp\"'" '-sGXX=aarch64-linux-gnu-g++  -march=armv8-a+crc -fstack-protector-strong -pie -fPIE -D_FORTIFY_SOURCE=2 -Wformat -Wformat-security -Werror=format-security --sysroot=YYY/recipe-sysroot '"'-DBOOST_PLATFORM_CONFIG=\"boost/config/platform/linux.hpp\"'" '-sGCC_INCLUDE_DIRECTORY=YYY/recipe-sysroot/usr/include' '-sGCC_STDLIB_DIRECTORY=YYY/recipe-sysroot/usr/lib64' '-sBUILD=release <optimization>space <threading>multi <inlining>on <debug-symbols>off' '-sPYTHON_ROOT=YYY/recipe-sysroot//usr' '--layout=system' -sBOOST_BUILD_USER_CONFIG=YYY/user-config.jam --build-dir=YYY --with-atomic --with-chrono --with-container --with-system --with-context --with-coroutine --with-date_time --with-fiber --with-filesystem --with-graph --with-iostreams --with-locale --with-log --with-math --with-random --with-regex --with-serialization --with-signals --with-stacktrace --with-test --with-program_options --with-thread --with-timer --with-type_erasure --with-wave address-model=64 architecture=arm toolset=gcc --prefix=/usr --exec-prefix=/usr --libdir=/usr/lib64 --includedir=/usr/include --debug-configuration --limit-tests=test'  
|  
ERROR: Task (XXX/recipes-support/boost/boost_1.66.0.bb:do_compile) failed with exit code '1'  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2022-02-27 01:24:28 UTC  
> Url: https://github.com/boostorg/test/issues/295#issuecomment-1052906388  

Is this still needed? your compiler is `gcc-gnu-4.3.1`, any chance you get a newer one?
