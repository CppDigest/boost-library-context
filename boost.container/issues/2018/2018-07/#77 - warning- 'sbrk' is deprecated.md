# #77 - warning: 'sbrk' is deprecated [Closed]

> Username: kivadiu  
> Created at: 2018-07-30 08:51:35 UTC  
> Updated at: 2018-11-11 00:39:43 UTC  
> Closed at: 2018-11-11 00:39:43 UTC  
> Url: https://github.com/boostorg/container/issues/77  

While building 1.68.0 beta 1 on x86_64 linux cross-compiling to OSX 10.9 with clang++ 5.0.2, I get this warning:  
```  
"x86_64-apple-darwin13-clang++-libc++" -x c -m64 -O3 -Wall -Wno-inline  -DBOOST_ALL_NO_LIB=1 -DBOOST_ASIO_NO_DEPRECATED -DBOOST_CONTAINER_STATIC_LINK=1 -DBOOST_FILESYSTEM_NO_DEPRECATED -DBOOST_LOG_WITHOUT_EVENT_LOG -DNDEBUG -I"." -c -o "bin.v2/libs/container/build/clang-darwin-5.0/release/link-static/target-os-darwin/threading-multi/alloc_lib.o" "libs/container/src/alloc_lib.c"  
In file included from libs/container/src/alloc_lib.c:24:  
In file included from libs/container/src/dlmalloc_ext_2_8_6.c:36:  
libs/container/src/dlmalloc_2_8_6.c:4085:27: warning: 'sbrk' is deprecated [-Wdeprecated-declarations]  
      char* base = (char*)CALL_MORECORE(0);  
                          ^  
libs/container/src/dlmalloc_2_8_6.c:1714:37: note: expanded from macro 'CALL_MORECORE'  
        #define CALL_MORECORE(S)    MORECORE_DEFAULT(S)  
                                    ^  
libs/container/src/dlmalloc_2_8_6.c:666:26: note: expanded from macro 'MORECORE_DEFAULT'  
#define MORECORE_DEFAULT sbrk  
                         ^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/unistd.h:580:1: note: 'sbrk' has been explicitly marked deprecated here  
__deprecated  
^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/sys/cdefs.h:156:37: note: expanded from macro '__deprecated'  
#define __deprecated    __attribute__((deprecated))  
                                       ^  
In file included from libs/container/src/alloc_lib.c:24:  
In file included from libs/container/src/dlmalloc_ext_2_8_6.c:36:  
libs/container/src/dlmalloc_2_8_6.c:4095:27: warning: 'sbrk' is deprecated [-Wdeprecated-declarations]  
            (br = (char*)(CALL_MORECORE(ssize))) == base) {  
                          ^  
libs/container/src/dlmalloc_2_8_6.c:1714:37: note: expanded from macro 'CALL_MORECORE'  
        #define CALL_MORECORE(S)    MORECORE_DEFAULT(S)  
                                    ^  
libs/container/src/dlmalloc_2_8_6.c:666:26: note: expanded from macro 'MORECORE_DEFAULT'  
#define MORECORE_DEFAULT sbrk  
                         ^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/unistd.h:580:1: note: 'sbrk' has been explicitly marked deprecated here  
__deprecated  
^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/sys/cdefs.h:156:37: note: expanded from macro '__deprecated'  
#define __deprecated    __attribute__((deprecated))  
                                       ^  
In file included from libs/container/src/alloc_lib.c:24:  
In file included from libs/container/src/dlmalloc_ext_2_8_6.c:36:  
libs/container/src/dlmalloc_2_8_6.c:4106:25: warning: 'sbrk' is deprecated [-Wdeprecated-declarations]  
          (br = (char*)(CALL_MORECORE(ssize))) == ss->base+ss->size) {  
                        ^  
libs/container/src/dlmalloc_2_8_6.c:1714:37: note: expanded from macro 'CALL_MORECORE'  
        #define CALL_MORECORE(S)    MORECORE_DEFAULT(S)  
                                    ^  
libs/container/src/dlmalloc_2_8_6.c:666:26: note: expanded from macro 'MORECORE_DEFAULT'  
#define MORECORE_DEFAULT sbrk  
                         ^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/unistd.h:580:1: note: 'sbrk' has been explicitly marked deprecated here  
__deprecated  
^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/sys/cdefs.h:156:37: note: expanded from macro '__deprecated'  
#define __deprecated    __attribute__((deprecated))  
                                       ^  
In file included from libs/container/src/alloc_lib.c:24:  
In file included from libs/container/src/dlmalloc_ext_2_8_6.c:36:  
libs/container/src/dlmalloc_2_8_6.c:4118:32: warning: 'sbrk' is deprecated [-Wdeprecated-declarations]  
            char* end = (char*)CALL_MORECORE(esize);  
                               ^  
libs/container/src/dlmalloc_2_8_6.c:1714:37: note: expanded from macro 'CALL_MORECORE'  
        #define CALL_MORECORE(S)    MORECORE_DEFAULT(S)  
                                    ^  
libs/container/src/dlmalloc_2_8_6.c:666:26: note: expanded from macro 'MORECORE_DEFAULT'  
#define MORECORE_DEFAULT sbrk  
                         ^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/unistd.h:580:1: note: 'sbrk' has been explicitly marked deprecated here  
__deprecated  
^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/sys/cdefs.h:156:37: note: expanded from macro '__deprecated'  
#define __deprecated    __attribute__((deprecated))  
                                       ^  
In file included from libs/container/src/alloc_lib.c:24:  
In file included from libs/container/src/dlmalloc_ext_2_8_6.c:36:  
libs/container/src/dlmalloc_2_8_6.c:4122:22: warning: 'sbrk' is deprecated [-Wdeprecated-declarations]  
              (void) CALL_MORECORE(-ssize);  
                     ^  
libs/container/src/dlmalloc_2_8_6.c:1714:37: note: expanded from macro 'CALL_MORECORE'  
        #define CALL_MORECORE(S)    MORECORE_DEFAULT(S)  
                                    ^  
libs/container/src/dlmalloc_2_8_6.c:666:26: note: expanded from macro 'MORECORE_DEFAULT'  
#define MORECORE_DEFAULT sbrk  
                         ^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/unistd.h:580:1: note: 'sbrk' has been explicitly marked deprecated here  
__deprecated  
^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/sys/cdefs.h:156:37: note: expanded from macro '__deprecated'  
#define __deprecated    __attribute__((deprecated))  
                                       ^  
In file included from libs/container/src/alloc_lib.c:24:  
In file included from libs/container/src/dlmalloc_ext_2_8_6.c:36:  
libs/container/src/dlmalloc_2_8_6.c:4153:20: warning: 'sbrk' is deprecated [-Wdeprecated-declarations]  
      br = (char*)(CALL_MORECORE(asize));  
                   ^  
libs/container/src/dlmalloc_2_8_6.c:1714:37: note: expanded from macro 'CALL_MORECORE'  
        #define CALL_MORECORE(S)    MORECORE_DEFAULT(S)  
                                    ^  
libs/container/src/dlmalloc_2_8_6.c:666:26: note: expanded from macro 'MORECORE_DEFAULT'  
#define MORECORE_DEFAULT sbrk  
                         ^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/unistd.h:580:1: note: 'sbrk' has been explicitly marked deprecated here  
__deprecated  
^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/sys/cdefs.h:156:37: note: expanded from macro '__deprecated'  
#define __deprecated    __attribute__((deprecated))  
                                       ^  
In file included from libs/container/src/alloc_lib.c:24:  
In file included from libs/container/src/dlmalloc_ext_2_8_6.c:36:  
libs/container/src/dlmalloc_2_8_6.c:4154:21: warning: 'sbrk' is deprecated [-Wdeprecated-declarations]  
      end = (char*)(CALL_MORECORE(0));  
                    ^  
libs/container/src/dlmalloc_2_8_6.c:1714:37: note: expanded from macro 'CALL_MORECORE'  
        #define CALL_MORECORE(S)    MORECORE_DEFAULT(S)  
                                    ^  
libs/container/src/dlmalloc_2_8_6.c:666:26: note: expanded from macro 'MORECORE_DEFAULT'  
#define MORECORE_DEFAULT sbrk  
                         ^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/unistd.h:580:1: note: 'sbrk' has been explicitly marked deprecated here  
__deprecated  
^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/sys/cdefs.h:156:37: note: expanded from macro '__deprecated'  
#define __deprecated    __attribute__((deprecated))  
                                       ^  
In file included from libs/container/src/alloc_lib.c:24:  
In file included from libs/container/src/dlmalloc_ext_2_8_6.c:36:  
libs/container/src/dlmalloc_2_8_6.c:4323:36: warning: 'sbrk' is deprecated [-Wdeprecated-declarations]  
            char* old_br = (char*)(CALL_MORECORE(0));  
                                   ^  
libs/container/src/dlmalloc_2_8_6.c:1714:37: note: expanded from macro 'CALL_MORECORE'  
        #define CALL_MORECORE(S)    MORECORE_DEFAULT(S)  
                                    ^  
libs/container/src/dlmalloc_2_8_6.c:666:26: note: expanded from macro 'MORECORE_DEFAULT'  
#define MORECORE_DEFAULT sbrk  
                         ^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/unistd.h:580:1: note: 'sbrk' has been explicitly marked deprecated here  
__deprecated  
^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/sys/cdefs.h:156:37: note: expanded from macro '__deprecated'  
#define __deprecated    __attribute__((deprecated))  
                                       ^  
In file included from libs/container/src/alloc_lib.c:24:  
In file included from libs/container/src/dlmalloc_ext_2_8_6.c:36:  
libs/container/src/dlmalloc_2_8_6.c:4325:38: warning: 'sbrk' is deprecated [-Wdeprecated-declarations]  
              char* rel_br = (char*)(CALL_MORECORE(-extra));  
                                     ^  
libs/container/src/dlmalloc_2_8_6.c:1714:37: note: expanded from macro 'CALL_MORECORE'  
        #define CALL_MORECORE(S)    MORECORE_DEFAULT(S)  
                                    ^  
libs/container/src/dlmalloc_2_8_6.c:666:26: note: expanded from macro 'MORECORE_DEFAULT'  
#define MORECORE_DEFAULT sbrk  
                         ^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/unistd.h:580:1: note: 'sbrk' has been explicitly marked deprecated here  
__deprecated  
^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/sys/cdefs.h:156:37: note: expanded from macro '__deprecated'  
#define __deprecated    __attribute__((deprecated))  
                                       ^  
In file included from libs/container/src/alloc_lib.c:24:  
In file included from libs/container/src/dlmalloc_ext_2_8_6.c:36:  
libs/container/src/dlmalloc_2_8_6.c:4326:38: warning: 'sbrk' is deprecated [-Wdeprecated-declarations]  
              char* new_br = (char*)(CALL_MORECORE(0));  
                                     ^  
libs/container/src/dlmalloc_2_8_6.c:1714:37: note: expanded from macro 'CALL_MORECORE'  
        #define CALL_MORECORE(S)    MORECORE_DEFAULT(S)  
                                    ^  
libs/container/src/dlmalloc_2_8_6.c:666:26: note: expanded from macro 'MORECORE_DEFAULT'  
#define MORECORE_DEFAULT sbrk  
                         ^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/unistd.h:580:1: note: 'sbrk' has been explicitly marked deprecated here  
__deprecated  
^  
/softs/osxcross-10.9-5.0.2/bin/../SDK/MacOSX10.9.sdk/usr/include/sys/cdefs.h:156:37: note: expanded from macro '__deprecated'  
#define __deprecated    __attribute__((deprecated))  
                                       ^  
10 warnings generated.  
```

---

## Comment 1

> Username: igaztanaga  
> Created at: 2018-11-11 00:20:10 UTC  
> Url: https://github.com/boostorg/container/issues/77#issuecomment-437632638  

Sorry for the extremely late reply. Reviewing dlmalloc code, I can't understand why it's happennig as in line 568 HAVE_MORECORE (sbrk) is disabled for MacOs:  
  
    #if defined(DARWIN) || defined(_DARWIN)  
    /* Mac OSX docs advise not to use sbrk; it seems better to use mmap */  
    #ifndef HAVE_MORECORE  
    #define HAVE_MORECORE 0  
    #define HAVE_MMAP 1

---

## Comment 2

> Username: igaztanaga  
> Created at: 2018-11-11 00:39:43 UTC  
> Url: https://github.com/boostorg/container/issues/77#issuecomment-437633623  

In any case, after reading that sbrk can weaken ASLR and other security tricks, I just disabled sbrk on all targets:  
  
https://github.com/boostorg/container/commit/e94c779b568e0866abe1411d0fcdff0039dde617  
  
Many thanks for the report.
