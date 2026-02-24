# #142 explicitly include <string.h> for memset() [Closed]

> Username: zv-io  
> Created at: 2017-08-26 05:35:00 UTC  
> Updated at: 2017-10-04 11:21:09 UTC  
> Closed at: 2017-08-27 08:59:42 UTC  
> Url: https://github.com/boostorg/fiber/pull/142  

Fixes:  
  
```  
In file included from /usr/include/pthread.h:30:0,  
                 from libs/fiber/src/numa/linux/pin_thread.cpp:10:  
libs/fiber/src/numa/linux/pin_thread.cpp: In function 'void boost::fibers::numa::pin_thread(uint32_t)':  
libs/fiber/src/numa/linux/pin_thread.cpp:27:5: error: 'memset' was not declared in this scope  
    CPU_ZERO( & set);  
    ^  
```

---

## Comment 1

> Username: olk  
> Created_at: 2017-08-26 15:44:17 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-325139695  

seams that `#define _GNU_SOURCE` is missing at the top of linux/pin_thread.cpp - could you check if it is working for you with this change?  
you could take a look at `man 3 CPU_ZERO`

---

## Comment 2

> Username: zv-io  
> Created_at: 2017-08-27 05:04:28 UTC  
> Updated_at: 2017-08-27 05:09:21 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-325177369  

Adding `#define _GNU_SOURCE` to the top of that file seems to produce a warning about re-definition of `_GNU_SOURCE`.  
  
    libs/fiber/src/numa/linux/pin_thread.cpp:8:0: warning: "_GNU_SOURCE" redefined  
     #define _GNU_SOURCE  
      
    <command-line>:0:0: note: this is the location of the previous definition  
    In file included from /usr/include/pthread.h:30:0,  
                     from libs/fiber/src/numa/linux/pin_thread.cpp:13:  
    libs/fiber/src/numa/linux/pin_thread.cpp: In function 'void boost::fibers::numa::pin_thread(uint32_t)':  
    libs/fiber/src/numa/linux/pin_thread.cpp:31:5: error: 'memset' was not declared in this scope  
         CPU_ZERO( & set);  
         ^  
Here is a minimal Dockerfile which exemplifies the problem: [Dockerfile.txt](https://github.com/boostorg/fiber/files/1254155/Dockerfile.txt)  
  
Adding the following line (effectively applying the patch) allows it to run to completion:  
  
            && sed -i libs/fiber/src/numa/linux/pin_thread.cpp \  
                -e '/sched.h/a #include <string.h>' \

---

## Comment 3

> Username: zv-io  
> Created_at: 2017-08-27 07:37:08 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-325182381  

Using `g++ -E -I. libs/fiber/src/numa/linux/pin_thread.cpp`  
  
    # 20 "libs/fiber/src/numa/linux/pin_thread.cpp"  
    namespace boost {  
    namespace fibers {  
    namespace numa {  
  
  
    void pin_thread( std::uint32_t cpuid) {  
        cpu_set_t set;  
  
    # 27 "libs/fiber/src/numa/linux/pin_thread.cpp" 3 4  
       memset(  
    # 27 "libs/fiber/src/numa/linux/pin_thread.cpp"  
       & set  
    # 27 "libs/fiber/src/numa/linux/pin_thread.cpp" 3 4  
       ,0,sizeof(cpu_set_t))  
    # 27 "libs/fiber/src/numa/linux/pin_thread.cpp"  
                       ;  
  
    # 28 "libs/fiber/src/numa/linux/pin_thread.cpp" 3 4  
       ( (  
    # 28 "libs/fiber/src/numa/linux/pin_thread.cpp"  
       cpuid  
    # 28 "libs/fiber/src/numa/linux/pin_thread.cpp" 3 4  
       )/8U >= (sizeof(cpu_set_t)) ? 0 : (((unsigned long *)(  
    # 28 "libs/fiber/src/numa/linux/pin_thread.cpp"  
       & set  
    # 28 "libs/fiber/src/numa/linux/pin_thread.cpp" 3 4  
       ))[(  
    # 28 "libs/fiber/src/numa/linux/pin_thread.cpp"  
       cpuid  
    # 28 "libs/fiber/src/numa/linux/pin_thread.cpp" 3 4  
       )/8/sizeof(long)] |= (1UL<<((  
    # 28 "libs/fiber/src/numa/linux/pin_thread.cpp"  
       cpuid  
    # 28 "libs/fiber/src/numa/linux/pin_thread.cpp" 3 4  
       )%(8*sizeof(long))))) )  
    # 28 "libs/fiber/src/numa/linux/pin_thread.cpp"  
                             ;  
        int err = 0;  
        if ( __builtin_expect(0 != ( err = ::pthread_setaffinity_np( ::pthread_self(), sizeof( set), & set) ), 0) ) {  
            throw std::system_error(  
                    std::error_code( err, std::system_category() ),  
                    "pthread_setaffinity_np() failed");  
        }  
    }  
  
    }}}  
  
OK. So inside `/usr/include/sched.h` I have the following (abbreviated):  
  
    #define CPU_ZERO_S(size,set) memset(set,0,size)  
    #define CPU_ZERO(set) CPU_ZERO_S(sizeof(cpu_set_t),set)  
  
And naturally, `memset()` is used here, defined in `string.h` but that is not included.  
  
How should we proceed? It seems to be a MUSL-libc issue, then: https://git.musl-libc.org/cgit/musl/tree/include/sched.h

---

## Comment 4

> Username: olk  
> Created_at: 2017-08-27 08:29:05 UTC  
> Updated_at: 2017-09-08 08:40:23 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-325184600  

hmm - my system uses the glibc. the `CPU_ZERO_S` is implemented in /`usr/include/bits/sched.h` using `__builtin_memset()`  
At the first look it seams to be a bug in MUSL-libc because the header should include `<string.h>` if it is using `memset()`

---

## Comment 5

> Username: zv-io  
> Created_at: 2017-08-27 08:38:21 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-325184990  

The MUSL FAQ ([link](https://www.musl-libc.org/faq.html)) says:  
  
> At both the source and binary level, musl aims for a degree of feature-compatibility, but not bug-compatibility, with glibc. When applications that work with glibc fail to compile against musl, the cause will usually be one of the following:  
>* Assuming that including one header will cause symbols from another unrelated header to be exposed. This is an application bug, and fixing it is as simple as adding the missing `#include` directives.  
> ...

---

## Comment 6

> Username: olk  
> Created_at: 2017-08-27 08:59:41 UTC  
> Updated_at: 2017-09-08 09:10:39 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-325186006  

It is a bug in MUSL - the macro expands to `memset()`. sched.h should include `<string.h>` to be self contained or add an forward declaration of `memset()`.

---

## Comment 7

> Username: zv-io  
> Created_at: 2017-08-27 09:55:59 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-325188550  

Respectfully, I must disagree with your decision for the following reasons:  
  
1. While some library header files may include others, this should not be relied upon. Currently, as you [mentioned](https://github.com/boostorg/fiber/pull/142#issuecomment-325184600), you have a dependency on glibc. Not all users of Boost choose to use it.  
  
2. It is better to only include headers where you need them, rather than have every file which uses `<sched.h>` also pull in `<string.h>` (in the case of requesting that MUSL is changed).  
  
3. The GNU libc page even states (1). See [here](https://www.gnu.org/software/libc/manual/html_node/Header-Files.html).  
  
> Some library header files include other library header files automatically. However, as a matter of programming style, you should not rely on this; it is better to explicitly include all the header files required for the library facilities you are using. The GNU C Library header files have been written in such a way that it doesn’t matter if a header file is accidentally included more than once; including a header file a second time has no effect. Likewise, if your program needs to include multiple header files, the order in which they are included doesn’t matter.  
  
4. This one file is the only issue in building the complete Boost 1.65 libraries on a MUSL-based system. After adding `#include <string.h>` to this file, it compiles (now) on both MUSL- and (still) glibc-based systems.

---

## Comment 8

> Username: olk  
> Created_at: 2017-08-27 10:16:02 UTC  
> Updated_at: 2017-09-08 08:41:51 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-325189444  

`man 3 CPU_ZERO` states that only header `sched.h` has to be included inc the user code:  
  
```  
#include <sched.h>  
void CPU_ZERO(cpu_set_t *set);  
```  
`sched.h` should be self contained - otherwise you would have to mess with all the libc implementations.  
why should you have to know which headers MUSIL libc needs but does not include in `sched.h`?  
 The same applies to other libc implementations - the user code would then be forced to include all headers for all libc implementations (or polute the header with conditional compilations ...). The order of the included headers would also be relevant (before `sched.h`).

---

## Comment 9

> Username: joerg-krause  
> Created_at: 2017-09-08 07:48:48 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-328028497  

I reported the issue on the musl mailing list: http://www.openwall.com/lists/musl/2017/09/08/1. Let's see, what the musl maintainers are saying...

---

## Comment 10

> Username: olk  
> Created_at: 2017-09-08 08:50:30 UTC  
> Updated_at: 2017-09-08 09:01:42 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-328043778  

in sched.h from MUSL `CPU_EQUAL_S` expands to `memcmp()`.  
`memcmp()` is forward declared in sched.h ... maybe the authors of MUSL forgot to add the forward declaration of `memset()`?  
`memcpy()` is also forward declared but never used in sched.h ... maybe confused with `memset()`?

---

## Comment 11

> Username: joerg-krause  
> Created_at: 2017-09-08 09:26:45 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-328052842  

You're right! Adding a forward declaration for `memset()` fixes the build issue. I reported your comment on the mailing list.

---

## Comment 12

> Username: olk  
> Created_at: 2017-09-08 11:13:07 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-328076250  

ty

---

## Comment 13

> Username: joerg-krause  
> Created_at: 2017-10-04 11:21:09 UTC  
> Url: https://github.com/boostorg/fiber/pull/142#issuecomment-334125019  

Note, that the issue has been fixed in musl: https://git.musl-libc.org/cgit/musl/commit/?id=48be5b6313d7b827acf555769e93b389fa9f6307

---
