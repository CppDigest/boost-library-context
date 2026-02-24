# #251 - UBSAN in test_tss [Open]

> Username: jeking3  
> Created at: 2018-11-20 19:16:04 UTC  
> Updated at: 2019-05-09 19:19:57 UTC  
> Url: https://github.com/boostorg/thread/issues/251  

Output: https://travis-ci.org/jeking3/thread/jobs/457490782#L1351  
```  
gcc.compile.c++ ../../bin.v2/libs/thread/test/test_xtime_lib.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/tss_null.o  
gcc.link ../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_tss  
testing.capture-output ../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_tss.run  
====== BEGIN OUTPUT ======  
../../libs/thread/src/pthread/thread.cpp:114:60: runtime error: member call on address 0x000001659b40 which does not point to an object of type 'tss_cleanup_function'  
0x000001659b40: note: object is of type 'boost::thread_specific_ptr<tss_value_t>::delete_data'  
 00 00 00 00  40 df 46 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  31 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::thread_specific_ptr<tss_value_t>::delete_data'  
    #0 0x7fe7adff437d in tls_destructor ../../libs/thread/src/pthread/thread.cpp:114  
    #1 0x7fe7adff487d in thread_proxy ../../libs/thread/src/pthread/thread.cpp:194  
    #2 0x7fe7ac1c2183 in start_thread (/lib/x86_64-linux-gnu/libpthread.so.0+0x8183)  
    #3 0x7fe7abeeeffc in clone (/lib/x86_64-linux-gnu/libc.so.6+0xfdffc)  
EXIT STATUS: 1  
====== END OUTPUT ======  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/chrono/build/gcc-8/debug/cxxstd-03-iso/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/system/build/gcc-8/debug/cxxstd-03-iso/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/test/build/gcc-8/debug/cxxstd-03-iso/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/thread/build/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/timer/build/gcc-8/debug/cxxstd-03-iso/threading-multi/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_tss"   > "../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_tss.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_tss.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_tss.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_tss.output" "../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_tss.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_tss.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
...failed testing.capture-output ../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_tss.run...  
```  
  
Discovered in Travis CI; repeatable using the [Boost Docker Development Environment](https://github.com/jeking3/bdde):  
```  
jking@ubuntu:~/boost/libs/thread/test$ bdde  
docker run -v /home/jking/boost:/boost:rw -v /home/jking/bdde:/bdde:ro --workdir /boost/libs/thread/test -it jeking3/bdde:linux /bin/bash  
boost@bdfbeceb79ff:/boost/libs/thread/test$ ubsan test_tss  
UBSAN_OPTIONS=print_stacktrace=1 b2 define=BOOST_NO_STRESS_TEST=1 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug toolset=gcc-8 test_tss  
Performing configuration checks  
  
    - default address-model    : 64-bit (cached)  
    - default architecture     : x86 (cached)  
    - symlinks supported       : yes (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : yes (cached)  
    - lockfree boost::atomic_flag : yes (cached)  
...patience...  
...patience...  
...found 3530 targets...  
...updating 10 targets...  
gcc.compile.c++ ../../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_tss.o  
gcc.compile.c++ ../../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/winrt_init.o  
gcc.link ../../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_tss  
testing.capture-output ../../../bin.v2/libs/thread/test/test_tss.test/gcc-8/debug/threadapi-pthread/threading-multi/visibility-hidden/test_tss.run  
====== BEGIN OUTPUT ======  
../../../libs/thread/src/pthread/thread.cpp:114:60: runtime error: member call on address 0x55ab4a8657e0 which does not point to an object of type 'tss_cleanup_function'  
0x55ab4a8657e0: note: object is of type 'boost::thread_specific_ptr<tss_value_t>::delete_data'  
 00 00 00 00  e0 2c ba 49 ab 55 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  31 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::thread_specific_ptr<tss_value_t>::delete_data'  
  
EXIT STATUS: 1  
====== END OUTPUT ======  
```  
  
test_11053 looks similar: https://travis-ci.org/jeking3/thread/jobs/457490782#L6358  
```  
gcc.compile.c++ ../../bin.v2/libs/thread/test/test_11053.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_11053.o  
gcc.link ../../bin.v2/libs/thread/test/test_11053.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_11053  
testing.capture-output ../../bin.v2/libs/thread/test/test_11053.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_11053.run  
====== BEGIN OUTPUT ======  
../../libs/thread/src/pthread/thread.cpp:114:60: runtime error: member call on address 0x000001feedc0 which does not point to an object of type 'tss_cleanup_function'  
0x000001feedc0: note: object is of type 'boost::thread_specific_ptr<B>::delete_data'  
 00 00 00 00  90 d2 42 00 00 00 00 00  00 00 00 00 00 00 00 00  00 00 00 00 00 00 00 00  31 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::thread_specific_ptr<B>::delete_data'  
    #0 0x7f0786cf937d in tls_destructor ../../libs/thread/src/pthread/thread.cpp:114  
    #1 0x7f0786cf987d in thread_proxy ../../libs/thread/src/pthread/thread.cpp:194  
    #2 0x7f078570e183 in start_thread (/lib/x86_64-linux-gnu/libpthread.so.0+0x8183)  
    #3 0x7f078543affc in clone (/lib/x86_64-linux-gnu/libc.so.6+0xfdffc)  
EXIT STATUS: 1  
====== END OUTPUT ======  
```

---

## Comment 1

> Username: viboes  
> Created at: 2018-11-21 21:06:13 UTC  
> Url: https://github.com/boostorg/thread/issues/251#issuecomment-440808118  

Would your PR fix this issue?

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-21 22:30:24 UTC  
> Url: https://github.com/boostorg/thread/issues/251#issuecomment-440830834  

No, I haven't addressed the UBSAN issues, just reported them.

---

## Comment 3

> Username: Kojoley  
> Created at: 2019-03-24 14:03:41 UTC  
> Updated at: 2019-03-24 14:04:27 UTC  
> Url: https://github.com/boostorg/thread/issues/251#issuecomment-475962579  

Duplicate of #236. Fixed in #249.
