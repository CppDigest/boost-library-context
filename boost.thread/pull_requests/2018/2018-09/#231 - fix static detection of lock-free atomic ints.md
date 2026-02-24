# #231 fix static detection of lock-free atomic ints [Merged]

> Username: ffontaine  
> Created at: 2018-09-05 10:24:41 UTC  
> Updated at: 2018-09-06 19:03:41 UTC  
> Merged at: 2018-09-06 19:03:20 UTC  
> Closed at: 2018-09-06 19:03:20 UTC  
> Url: https://github.com/boostorg/thread/pull/231  

When build statically, boost is unable to detect lock-free atomics ints  
because it tries to link dynamically with boost_system, see  
output/build/boost-1.67.0/bin.v2/config.log (with -d5 option):  
  
Using shell: /bin/sh -c  
    argv[0] = '/bin/sh'  
    argv[1] = '-c'  
    argv[2] = '  
    "/home/fabrice/buildroot/output/host/bin/arm-linux-g++"   -D_LARGEFILE_SOURCE -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64  -Os   -Wl,-elf2flt -static  -Wl,-elf2flt -static -fPIC -pthread -O0 -fno-inline -Wall -pedantic -g -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1  -I"." -c -o "bin.v2/libs/thread/build/gcc-6.4.0/debug/threading-multi/has_atomic_flag_lockfree_test.o" "libs/thread/src/../build/has_atomic_flag_lockfree_test.cpp"  
'  
gcc.compile.c++ bin.v2/libs/thread/build/gcc-6.4.0/debug/threading-multi/has_atomic_flag_lockfree_test.o  
  
    "/home/fabrice/buildroot/output/host/bin/arm-linux-g++"   -D_LARGEFILE_SOURCE -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64  -Os   -Wl,-elf2flt -static  -Wl,-elf2flt -static -fPIC -pthread -O0 -fno-inline -Wall -pedantic -g -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1  -I"." -c -o "bin.v2/libs/thread/build/gcc-6.4.0/debug/threading-multi/has_atomic_flag_lockfree_test.o" "libs/thread/src/../build/has_atomic_flag_lockfree_test.cpp"  
  
0.033561 sec system; 0.126314 sec user; 288.682473 sec clock  
gcc.compile.c++ bin.v2/libs/system/build/gcc-6.4.0/debug/threading-multi/error_code.o  
  
    "/home/fabrice/buildroot/output/host/bin/arm-linux-g++"   -D_LARGEFILE_SOURCE -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64  -Os   -Wl,-elf2flt -static  -Wl,-elf2flt -static -fPIC -pthread -O0 -fno-inline -Wall -pedantic -g  -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1  -I"." -c -o "bin.v2/libs/system/build/gcc-6.4.0/debug/threading-multi/error_code.o" "libs/system/src/error_code.cpp"  
  
0.084060 sec system; 0.644133 sec user; 8.858824 sec clock  
SEM: <s>gcc-link-semaphore now used by <pbin.v2/libs/system/build/gcc-6.4.0/debug/threading-multi>libboost_system.so.1.67.0  
Using shell: /bin/sh -c  
    argv[0] = '/bin/sh'  
    argv[1] = '-c'  
    argv[2] = '  
    "/home/fabrice/buildroot/output/host/bin/arm-linux-g++"    -o "bin.v2/libs/system/build/gcc-6.4.0/debug/threading-multi/libboost_system.so.1.67.0" -Wl,-h -Wl,libboost_system.so.1.67.0 -shared -Wl,--start-group "bin.v2/libs/system/build/gcc-6.4.0/debug/threading-multi/error_code.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -pthread -g  -Wl,-elf2flt -static  
'  
gcc.link.dll bin.v2/libs/system/build/gcc-6.4.0/debug/threading-multi/libboost_system.so.1.67.0  
  
    "/home/fabrice/buildroot/output/host/bin/arm-linux-g++"    -o "bin.v2/libs/system/build/gcc-6.4.0/debug/threading-multi/libboost_system.so.1.67.0" -Wl,-h -Wl,libboost_system.so.1.67.0 -shared -Wl,--start-group "bin.v2/libs/system/build/gcc-6.4.0/debug/threading-multi/error_code.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -pthread -g  -Wl,-elf2flt -static  
  
ld (ld-elf2flt): -shared used without passing a shared library ID  
collect2: error: ld a retourné le statut de sortie 1  
0.003123 sec system; 0.004732 sec user; 15.646509 sec clock  
...failed gcc.link.dll bin.v2/libs/system/build/gcc-6.4.0/debug/threading-multi/libboost_system.so.1.67.0...  
  
To fix this, move the exe statement before the boost/thread project  
  
Fixes:  
 - http://autobuild.buildroot.org/results/f46d38991385cbc2a4fa14eb31074e770cd79803  
  
Signed-off-by: Fabrice Fontaine <fontaine.fabrice@gmail.com>

---

## Comment 1

> Username: viboes  
> Created_at: 2018-09-06 04:55:35 UTC  
> Url: https://github.com/boostorg/thread/pull/231#issuecomment-418964462  

I don't master b2, but it is weird that changing the order solves anything.

---

## Comment 2

> Username: ffontaine  
> Created_at: 2018-09-06 12:20:44 UTC  
> Url: https://github.com/boostorg/thread/pull/231#issuecomment-419073080  

I don't master b2 either but the order seems to be important.  
Here is an extract of bin.v2/config.log with the following invocation bjam link=static runtime-link=static -a -d5:  
  
```  
gcc.compile.c++ bin.v2/libs/thread/build/gcc-5.4.0/debug/threading-multi/has_atomic_flag_lockfree_test.o  
  
    "g++"   -fPIC -m64 -pthread -O0 -fno-inline -Wall -pedantic -g -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1  -I"." -c -o "bin.v2/libs/thread/build/gcc-5.4.0/debug/threading-multi/has_atomic_flag_lockfree_test.o" "libs/thread/src/../build/has_atomic_flag_lockfree_test.cpp"  
  
Using shell: /bin/sh -c  
    argv[0] = '/bin/sh'  
    argv[1] = '-c'  
    argv[2] = '  
    "g++"   -fPIC -m64 -pthread -O0 -fno-inline -Wall -pedantic -g -Wextra -Wno-long-long -Wno-unused-parameter -Wunused-function -pedantic -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1 -DBOOST_THREAD_BUILD_DLL=1  -I"." -c -o "bin.v2/libs/thread/build/gcc-5.4.0/debug/threading-multi/has_atomic_flag_lockfree_test.o" "libs/thread/src/../build/has_atomic_flag_lockfree_test.cpp"  
'  
0.008000 sec system; 0.072000 sec user; 85.168982 sec clock  
gcc.compile.c++ bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi/error_code.o  
  
    "g++"   -fPIC -m64 -pthread -O0 -fno-inline -Wall -pedantic -g  -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1  -I"." -c -o "bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi/error_code.o" "libs/system/src/error_code.cpp"  
  
Using shell: /bin/sh -c  
    argv[0] = '/bin/sh'  
    argv[1] = '-c'  
    argv[2] = '  
    "g++"   -fPIC -m64 -pthread -O0 -fno-inline -Wall -pedantic -g  -DBOOST_ALL_NO_LIB=1 -DBOOST_SYSTEM_DYN_LINK=1  -I"." -c -o "bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi/error_code.o" "libs/system/src/error_code.cpp"  
'  
0.000000 sec system; 0.184000 sec user; 190.668579 sec clock  
SEM: <s>gcc-link-semaphore now used by <pbin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi>libboost_system.so.1.68.0  
gcc.link.dll bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi/libboost_system.so.1.68.0  
  
    "g++"    -o "bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi/libboost_system.so.1.68.0" -Wl,-h -Wl,libboost_system.so.1.68.0 -shared -Wl,--start-group "bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi/error_code.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -g  
  
Using shell: /bin/sh -c  
    argv[0] = '/bin/sh'  
    argv[1] = '-c'  
    argv[2] = '  
    "g++"    -o "bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi/libboost_system.so.1.68.0" -Wl,-h -Wl,libboost_system.so.1.68.0 -shared -Wl,--start-group "bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi/error_code.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -g  
'  
0.012000 sec system; 0.028000 sec user; 46.352284 sec clock  
SEM: <s>gcc-link-semaphore is now free  
SEM: <s>gcc-link-semaphore now used by <pbin.v2/libs/thread/build/gcc-5.4.0/debug/threading-multi>has_atomic_flag_lockfree  
gcc.link bin.v2/libs/thread/build/gcc-5.4.0/debug/threading-multi/has_atomic_flag_lockfree  
  
    "g++"  -Wl,-rpath -Wl,"/home/uffo8396/Téléchargements/boost_1_68_0/bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi" -Wl,-rpath-link -Wl,"/home/uffo8396/Téléchargements/boost_1_68_0/bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi" -o "bin.v2/libs/thread/build/gcc-5.4.0/debug/threading-multi/has_atomic_flag_lockfree" -Wl,--start-group "bin.v2/libs/thread/build/gcc-5.4.0/debug/threading-multi/has_atomic_flag_lockfree_test.o" "bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi/libboost_system.so.1.68.0"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -g  
  
Using shell: /bin/sh -c  
    argv[0] = '/bin/sh'  
    argv[1] = '-c'  
    argv[2] = '  
    "g++"  -Wl,-rpath -Wl,"/home/uffo8396/Téléchargements/boost_1_68_0/bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi" -Wl,-rpath-link -Wl,"/home/uffo8396/Téléchargements/boost_1_68_0/bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi" -o "bin.v2/libs/thread/build/gcc-5.4.0/debug/threading-multi/has_atomic_flag_lockfree" -Wl,--start-group "bin.v2/libs/thread/build/gcc-5.4.0/debug/threading-multi/has_atomic_flag_lockfree_test.o" "bin.v2/libs/system/build/gcc-5.4.0/debug/threading-multi/libboost_system.so.1.68.0"  -Wl,-Bstatic  -Wl,-Bdynamic -lrt -Wl,--end-group -fPIC -m64 -pthread -g  
```  
You can see that to link has_atomic_flag_lockfree_test, boost thread assumes that it must build and link wiith libboost_system.so even we have specify in the arguments of bjam that we wanted static linking. This fails on an error if the compiler does not support dynamic linking.  
  
Now, the same output after moving the line before the project boost/thread statement:  
  
```  
gcc.compile.c++ bin.v2/libs/thread/build/gcc-5.4.0/debug/has_atomic_flag_lockfree_test.o  
  
    "g++"   -fPIC -m64 -O0 -fno-inline -Wall -g  -DBOOST_ALL_NO_LIB=1  -I"." -c -o "bin.v2/libs/thread/build/gcc-5.4.0/debug/has_atomic_flag_lockfree_test.o" "libs/thread/src/../build/has_atomic_flag_lockfree_test.cpp"  
  
Using shell: /bin/sh -c  
    argv[0] = '/bin/sh'  
    argv[1] = '-c'  
    argv[2] = '  
    "g++"   -fPIC -m64 -O0 -fno-inline -Wall -g  -DBOOST_ALL_NO_LIB=1  -I"." -c -o "bin.v2/libs/thread/build/gcc-5.4.0/debug/has_atomic_flag_lockfree_test.o" "libs/thread/src/../build/has_atomic_flag_lockfree_test.cpp"  
'  
0.020000 sec system; 0.060000 sec user; 86.479708 sec clock  
SEM: <s>gcc-link-semaphore now used by <pbin.v2/libs/thread/build/gcc-5.4.0/debug>has_atomic_flag_lockfree  
gcc.link bin.v2/libs/thread/build/gcc-5.4.0/debug/has_atomic_flag_lockfree  
  
    "g++"    -o "bin.v2/libs/thread/build/gcc-5.4.0/debug/has_atomic_flag_lockfree" -Wl,--start-group "bin.v2/libs/thread/build/gcc-5.4.0/debug/has_atomic_flag_lockfree_test.o"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -fPIC -m64 -g  
  
Using shell: /bin/sh -c  
    argv[0] = '/bin/sh'  
    argv[1] = '-c'  
    argv[2] = '  
    "g++"    -o "bin.v2/libs/thread/build/gcc-5.4.0/debug/has_atomic_flag_lockfree" -Wl,--start-group "bin.v2/libs/thread/build/gcc-5.4.0/debug/has_atomic_flag_lockfree_test.o"  -Wl,-Bstatic  -Wl,-Bdynamic  -Wl,--end-group -fPIC -m64 -g  
'  
```  
  
You can see that now has_atomic_flag_lockfree does not link with libboost_system and that's a good thing as this test does not need this library.  
  
I know that a better solution would be to understand why static linking is not used when building and linking libboost_system with has_atomic_flag_lockfree but it is beyond my current skills in b2.

---

## Comment 3

> Username: viboes  
> Created_at: 2018-09-06 19:03:40 UTC  
> Url: https://github.com/boostorg/thread/pull/231#issuecomment-419206681  

I've merged it. We will see how it behaves.

---
