# #250 - UBSAN in test_thread_exit [Open]

> Username: jeking3  
> Created at: 2018-11-20 19:12:45 UTC  
> Updated at: 2019-05-09 19:20:19 UTC  
> Url: https://github.com/boostorg/thread/issues/250  

Output: https://travis-ci.org/jeking3/thread/jobs/457490782#L1226  
```  
gcc.compile.c++ ../../bin.v2/libs/thread/test/test_thread_exit_lib.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/winrt_init.o  
gcc.compile.c++ ../../bin.v2/libs/thread/test/test_thread_exit_lib.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/tss_null.o  
gcc.compile.c++ ../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.o  
gcc.link ../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit  
testing.capture-output ../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.run  
====== BEGIN OUTPUT ======  
../../libs/thread/src/pthread/thread.cpp:103:58: runtime error: member call on address 0x7f2ce80008c0 which does not point to an object of type 'thread_exit_function_base'  
0x7f2ce80008c0: note: object is of type 'boost::detail::thread_exit_function<void (*)()>'  
 00 00 00 00  d0 f2 42 00 00 00 00 00  94 55 40 00 00 00 00 00  00 00 00 00 00 00 00 00  25 00 00 00  
              ^~~~~~~~~~~~~~~~~~~~~~~  
              vptr for 'boost::detail::thread_exit_function<void (*)()>'  
    #0 0x7f2cf1084d90 in tls_destructor ../../libs/thread/src/pthread/thread.cpp:103  
    #1 0x7f2cf108587d in thread_proxy ../../libs/thread/src/pthread/thread.cpp:194  
    #2 0x7f2cef45b183 in start_thread (/lib/x86_64-linux-gnu/libpthread.so.0+0x8183)  
    #3 0x7f2cef187ffc in clone (/lib/x86_64-linux-gnu/libc.so.6+0xfdffc)  
EXIT STATUS: 1  
====== END OUTPUT ======  
    LD_LIBRARY_PATH="/home/travis/build/jeking3/boost-root/bin.v2/libs/chrono/build/gcc-8/debug/cxxstd-03-iso/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/system/build/gcc-8/debug/cxxstd-03-iso/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/test/build/gcc-8/debug/cxxstd-03-iso/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/thread/build/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden:/home/travis/build/jeking3/boost-root/bin.v2/libs/timer/build/gcc-8/debug/cxxstd-03-iso/threading-multi/visibility-hidden:/usr/bin:/usr/lib:/usr/lib32:/usr/lib64:$LD_LIBRARY_PATH"  
export LD_LIBRARY_PATH  
    status=0  
    if test $status -ne 0 ; then  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    fi  
     "../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit"   > "../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output" 2>&1 < /dev/null  
    status=$?  
    echo >> "../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output"  
    echo EXIT STATUS: $status >> "../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output"  
    if test $status -eq 0 ; then  
        cp "../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output" "../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.run"  
    fi  
    verbose=0  
    if test $status -ne 0 ; then  
        verbose=1  
    fi  
    if test $verbose -eq 1 ; then  
        echo ====== BEGIN OUTPUT ======  
        cat "../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.output"  
        echo ====== END OUTPUT ======  
    fi  
    exit $status  
...failed testing.capture-output ../../bin.v2/libs/thread/test/test_thread_exit.test/gcc-8/debug/cxxstd-03-iso/threadapi-pthread/threading-multi/visibility-hidden/test_thread_exit.run...  
```  
  
Repeatable with the following command using the [Boost Docker Development Environment](https://github.com/jeking3/bdde):  
```  
jking@ubuntu:~/boost/libs/thread/test$ bdde  
docker run -v /home/jking/boost:/boost:rw -v /home/jking/bdde:/bdde:ro --workdir /boost/libs/thread/test -it jeking3/bdde:linux /bin/bash  
  
boost@907e1fe89581:/boost/libs/thread/test$ ubsan test_thread_exit  
UBSAN_OPTIONS=print_stacktrace=1 b2 define=BOOST_NO_STRESS_TEST=1 cxxflags=-fno-omit-frame-pointer cxxflags=-fsanitize=undefined cxxflags=-fno-sanitize-recover=undefined linkflags=-fsanitize=undefined linkflags=-fno-sanitize-recover=undefined linkflags=-fuse-ld=gold variant=debug toolset=gcc-8 test_thread_exit  
```

---

## Comment 1

> Username: viboes  
> Created at: 2018-11-21 21:05:25 UTC  
> Url: https://github.com/boostorg/thread/issues/250#issuecomment-440807951  

Would your PR fix this issue?

---

## Comment 2

> Username: jeking3  
> Created at: 2018-11-21 22:30:11 UTC  
> Url: https://github.com/boostorg/thread/issues/250#issuecomment-440830794  

No, I haven't addressed the UBSAN issues, just reported them.

---

## Comment 3

> Username: viboes  
> Created at: 2018-11-25 07:53:06 UTC  
> Url: https://github.com/boostorg/thread/issues/250#issuecomment-441422215  

On which platform is this test running and how to know it?

---

## Comment 4

> Username: jeking3  
> Created at: 2018-11-25 13:00:14 UTC  
> Url: https://github.com/boostorg/thread/issues/250#issuecomment-441438800  

Ubuntu Bionic, using gcc-8.  The boost docker development environment makes it easier to reproduce.  See the last past of the description.

---

## Comment 5

> Username: viboes  
> Created at: 2018-11-26 06:17:55 UTC  
> Url: https://github.com/boostorg/thread/issues/250#issuecomment-441530404  

I'm asking because it is not clear from the travis jobs titles
