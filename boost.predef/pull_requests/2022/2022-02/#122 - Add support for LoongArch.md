# #122 Add support for LoongArch [Merged]

> Username: loongson-zn  
> Created at: 2022-02-14 11:30:47 UTC  
> Updated at: 2022-02-26 03:02:33 UTC  
> Merged at: 2022-02-26 03:02:32 UTC  
> Closed at: 2022-02-26 03:02:32 UTC  
> Url: https://github.com/boostorg/predef/pull/122  

Add support for LoongArch, please review, thanks!  
  
Signed-off-by: Zhang Na [zhangna@loongson.cn](mailto:zhangna@loongson.cn)  
Reviewed-by: Wang Xuerui [git@xen0n.name](mailto:git@xen0n.name)  
Reviewed-by: Wu Xiaotian [wuxiaotian@loongson.cn](mailto:wuxiaotian@loongson.cn)  
Reviewed-by: Zhu Yaliang [zhuyaliang@loongson.cn](mailto:zhuyaliang@loongson.cn)

---

## Comment 1

> Username: loongson-zn  
> Created_at: 2022-02-24 03:52:08 UTC  
> Updated_at: 2022-02-24 03:53:39 UTC  
> Url: https://github.com/boostorg/predef/pull/122#issuecomment-1049467927  

test result:  
```  
zn@la-11:~/github/boost-zn/libs/predef/test$ arch  
loongarch64  
zn@la-11:~/github/boost-zn/libs/predef/test$ ../../../b2   
Performing configuration checks  
  
    - default address-model    : 64-bit (cached) [1]  
    - default architecture     : loongarch (cached) [1]  
    - symlinks supported       : yes (cached)  
    - BOOST_OS_MACOS           : no  (cached) [2]  
    - BOOST_OS_MACOS           : no  (cached) [3]  
    - BOOST_COMP_CLANG > 0.0.0 : no  (cached) [2]  
    - BOOST_OS_LINUX == 0      : no  (cached) [2]  
  
[1] gcc-8  
[2] gcc-8/debug/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
[3] gcc-8/debug/build-no/python-2.7/threadapi-pthread/threading-multi/visibility-hidden  
...patience...  
...found 441 targets...  
...updating 35 targets...  
gcc.compile.c ../../../bin.v2/libs/predef/test/info_as_c.test/gcc-8/debug/threading-multi/visibility-hidden/info_as_c.o  
gcc.compile.c++ ../../../bin.v2/libs/predef/test/info_as_cpp.test/gcc-8/debug/threading-multi/visibility-hidden/info_as_cpp.o  
gcc.link ../../../bin.v2/libs/predef/test/info_as_c.test/gcc-8/debug/threading-multi/visibility-hidden/info_as_c  
gcc.link ../../../bin.v2/libs/predef/test/info_as_cpp.test/gcc-8/debug/threading-multi/visibility-hidden/info_as_cpp  
testing.capture-output ../../../bin.v2/libs/predef/test/info_as_c.test/gcc-8/debug/threading-multi/visibility-hidden/info_as_c.run  
**passed** ../../../bin.v2/libs/predef/test/info_as_c.test/gcc-8/debug/threading-multi/visibility-hidden/info_as_c.test  
testing.capture-output ../../../bin.v2/libs/predef/test/info_as_cpp.test/gcc-8/debug/threading-multi/visibility-hidden/info_as_cpp.run  
**passed** ../../../bin.v2/libs/predef/test/info_as_cpp.test/gcc-8/debug/threading-multi/visibility-hidden/info_as_cpp.test  
gcc.compile.c++ ../../../bin.v2/libs/predef/test/version.test/gcc-8/debug/threading-multi/visibility-hidden/version.o  
gcc.compile.c++ ../../../bin.v2/libs/predef/test/make.test/gcc-8/debug/threading-multi/visibility-hidden/make.o  
gcc.link ../../../bin.v2/libs/predef/test/version.test/gcc-8/debug/threading-multi/visibility-hidden/version  
testing.capture-output ../../../bin.v2/libs/predef/test/version.test/gcc-8/debug/threading-multi/visibility-hidden/version.run  
**passed** ../../../bin.v2/libs/predef/test/version.test/gcc-8/debug/threading-multi/visibility-hidden/version.test  
gcc.compile.c++ ../../../bin.v2/libs/predef/test/workaround_strict_config.test/gcc-8/debug/threading-multi/visibility-hidden/workaround_strict_config.o  
**passed** ../../../bin.v2/libs/predef/test/workaround_strict_config.test/gcc-8/debug/threading-multi/visibility-hidden/workaround_strict_config.test  
gcc.link ../../../bin.v2/libs/predef/test/make.test/gcc-8/debug/threading-multi/visibility-hidden/make  
gcc.compile.c++ ../../../bin.v2/libs/predef/test/check_value.test/gcc-8/debug/threading-multi/visibility-hidden/check_value.o  
testing.capture-output ../../../bin.v2/libs/predef/test/make.test/gcc-8/debug/threading-multi/visibility-hidden/make.run  
**passed** ../../../bin.v2/libs/predef/test/make.test/gcc-8/debug/threading-multi/visibility-hidden/make.test  
gcc.compile.c++ ../../../bin.v2/libs/predef/test/tested_at_outdated.test/gcc-8/debug/threading-multi/visibility-hidden/tested_at_outdated.o  
In file included from tested_at_outdated.cpp:9:  
../../../boost/predef/other/workaround.h:88:17: error: division by zero in #if  
             : (1%0) )  
                 ^  
tested_at_outdated.cpp:13:5: note: in expansion of macro ‘BOOST_PREDEF_TESTED_AT’  
 #if BOOST_PREDEF_TESTED_AT(BOOST_VERSION_NUMBER(2,0,0),1,0,0)  
     ^~~~~~~~~~~~~~~~~~~~~~  
(failed-as-expected) ../../../bin.v2/libs/predef/test/tested_at_outdated.test/gcc-8/debug/threading-multi/visibility-hidden/tested_at_outdated.o  
**passed** ../../../bin.v2/libs/predef/test/tested_at_outdated.test/gcc-8/debug/threading-multi/visibility-hidden/tested_at_outdated.test  
gcc.compile.c++ ../../../bin.v2/libs/predef/test/platform_windows.test/gcc-8/debug/threading-multi/visibility-hidden/platform_windows.o  
**passed** ../../../bin.v2/libs/predef/test/platform_windows.test/gcc-8/debug/threading-multi/visibility-hidden/platform_windows.test  
gcc.link ../../../bin.v2/libs/predef/test/check_value.test/gcc-8/debug/threading-multi/visibility-hidden/check_value  
testing.capture-output ../../../bin.v2/libs/predef/test/check_value.test/gcc-8/debug/threading-multi/visibility-hidden/check_value.run  
**passed** ../../../bin.v2/libs/predef/test/check_value.test/gcc-8/debug/threading-multi/visibility-hidden/check_value.test  
gcc.compile.c++ ../../../bin.v2/libs/predef/test/workaround.test/gcc-8/debug/threading-multi/visibility-hidden/workaround.o  
gcc.link ../../../bin.v2/libs/predef/test/workaround.test/gcc-8/debug/threading-multi/visibility-hidden/workaround  
testing.capture-output ../../../bin.v2/libs/predef/test/workaround.test/gcc-8/debug/threading-multi/visibility-hidden/workaround.run  
**passed** ../../../bin.v2/libs/predef/test/workaround.test/gcc-8/debug/threading-multi/visibility-hidden/workaround.test  
gcc.compile.c++ ../../../bin.v2/libs/predef/test/tested_at.test/gcc-8/debug/threading-multi/visibility-hidden/tested_at.o  
gcc.link ../../../bin.v2/libs/predef/test/tested_at.test/gcc-8/debug/threading-multi/visibility-hidden/tested_at  
testing.capture-output ../../../bin.v2/libs/predef/test/tested_at.test/gcc-8/debug/threading-multi/visibility-hidden/tested_at.run  
**passed** ../../../bin.v2/libs/predef/test/tested_at.test/gcc-8/debug/threading-multi/visibility-hidden/tested_at.test  
...updated 35 targets...  
  
```

---
