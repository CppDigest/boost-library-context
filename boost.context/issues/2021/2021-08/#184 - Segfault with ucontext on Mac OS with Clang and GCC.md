# #184 - Segfault with ucontext on Mac OS with Clang and GCC [Closed]

> Username: francoisk  
> Created at: 2021-08-11 14:04:42 UTC  
> Updated at: 2022-12-17 10:39:22 UTC  
> Closed at: 2022-12-17 10:39:21 UTC  
> Url: https://github.com/boostorg/context/issues/184  

All of the examples I tried (examples/fiber/fibonacci, for one) segfault on Mac OS with Clang and GCC if Context is built using ucontext.  
  
The tested configurations are:  
- Mac OS 10.15, Clang 11.0  
- Mac OS 11, Clang 11.1 and 12  
- Mac OS 11, GCC 11.1  
  
These configurations were tested under a different build system (which I ported to myself) but I did test the Boost build on Mac OS 11 with Clang 12 and had the same results. The Fibonacci example produced the correct output on Linux, FreeBSD, and Windows under the ported build.  
  
The build commands I used are:  
  
```  
  b2 toolset=clang cxxflags='-std=c++11' --with-context context-impl=ucontext stage  
  cd libs/context/examples/fiber  
  b2 toolset=clang cxxflags='-std=c++11' context-impl=ucontext  
```

---

## Comment 1

> Username: olk  
> Created at: 2021-08-11 14:37:59 UTC  
> Updated at: 2021-08-11 14:38:17 UTC  
> Url: https://github.com/boostorg/context/issues/184#issuecomment-896883930  

Don't own a MacOS system - without a stack trace I can't do anything.

---

## Comment 2

> Username: francoisk  
> Created at: 2021-08-18 07:39:58 UTC  
> Url: https://github.com/boostorg/context/issues/184#issuecomment-900892580  

Hope this helps. On Mac OS 11 with Clang 12.0:  
  
```  
* thread #1, queue = 'com.apple.main-thread', stop reason = EXC_BAD_ACCESS (code=1, address=0x10040458e)  
  * frame #0: 0x00007fff2013cc94 libsystem_malloc.dylib`tiny_free_list_add_ptr + 122  
    frame #1: 0x00007fff2013a3ff libsystem_malloc.dylib`tiny_malloc_from_free_list + 1632  
    frame #2: 0x00007fff2013981d libsystem_malloc.dylib`tiny_malloc_should_clear + 233  
    frame #3: 0x00007fff20138727 libsystem_malloc.dylib`szone_malloc_should_clear + 66  
    frame #4: 0x00007fff20151fe5 libsystem_malloc.dylib`_malloc_zone_malloc + 118  
    frame #5: 0x00007fff2032940d libdyld.dylib`_tlv_atexit + 172  
    frame #6: 0x0000000100006a18 fibonacci`boost::context::detail::fiber_activation_record::current() at fiber.cpp:48:5  
    frame #7: 0x00000001000049d9 fibonacci`boost::context::detail::fiber_activation_record::resume(this=0x0000000100170f00) at fiber_ucontext.hpp:131:10  
    frame #8: 0x0000000100004755 fibonacci`boost::context::fiber::resume(this=0x00007ffeefbff900) && at fiber_ucontext.hpp:471:83  
    frame #9: 0x000000010000457a fibonacci`main at fibonacci.cpp:30:27  
    frame #10: 0x00007fff2032bf5d libdyld.dylib`start + 1  
```

---

## Comment 3

> Username: olk  
> Created at: 2021-10-07 08:13:43 UTC  
> Url: https://github.com/boostorg/context/issues/184#issuecomment-937559391  

no idea, I don't have access to a Mac system so I can't debug it

---

## Comment 4

> Username: boris-kolpackov  
> Created at: 2021-10-21 07:49:53 UTC  
> Url: https://github.com/boostorg/context/issues/184#issuecomment-948349881  

We can organize an interactive CI session (i.e., a VNC or SSH login) if you would like to debug this.

---

## Comment 5

> Username: elfprince13  
> Created at: 2022-07-07 13:31:26 UTC  
> Url: https://github.com/boostorg/context/issues/184#issuecomment-1177619758  

I'm wondering if this is related to #198.

---

## Comment 6

> Username: eduardvoronkin  
> Created at: 2022-12-16 01:41:08 UTC  
> Url: https://github.com/boostorg/context/issues/184#issuecomment-1354058568  

here I've addressed the problem https://github.com/boostorg/context/pull/213

---

## Comment 7

> Username: olk  
> Created at: 2022-12-17 10:39:21 UTC  
> Url: https://github.com/boostorg/context/issues/184#issuecomment-1356167329  

should be fixed by "fix ucontext for MacOS #213 "
