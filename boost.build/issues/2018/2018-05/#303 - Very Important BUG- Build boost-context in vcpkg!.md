# #303 - Very Important BUG:  Build boost-context in vcpkg! [Closed]

> Username: yyzybb537  
> Created at: 2018-05-05 09:18:41 UTC  
> Updated at: 2018-05-20 04:59:12 UTC  
> Closed at: 2018-05-20 04:59:12 UTC  
> Url: https://github.com/boostorg/build/issues/303  

The libboost_context.a missing symbols:  
make_fcontext  
jump_fcontext  
ontop_fcontext  
  
when boost.context built by vcpkg.  
I think your build script what you committed, lose the asm files in vcpkg.

---

## Comment 1

> Username: vprus  
> Created at: 2018-05-05 09:24:32 UTC  
> Url: https://github.com/boostorg/build/issues/303#issuecomment-386792447  

Thanks for your bug report! Could you provide some context? The Boost.Build team is not working on vcpkg integration and is not responsible for boost.context. Is there anything that Boost.Build is doing incorrectly? If not, maybe boost.context author or vcpkg package author will be better positioned to fix the problem.

---

## Comment 2

> Username: yyzybb537  
> Created at: 2018-05-06 02:50:45 UTC  
> Url: https://github.com/boostorg/build/issues/303#issuecomment-386849683  

OS: linux  
Version: ubuntu16.04  
Pkg Manager: vcpkg （https://github.com/Microsoft/vcpkg）  
  
The command that vcpkg generated for compile lib boost.context as:  
  
/home/ubuntu/git-third/vcpkg/installed/x64-linux/tools/boost-build/b2  
--user-config=/home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel/user-config.jam  
--stagedir=/home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel/stage  
--build-dir=/home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel  
runtime-link=static;link=static;target-os=linux;toolset=gcc;-sZLIB_BINARY=z;-sZLIB_LIBPATH="/home/ubuntu/git-third/vcpkg/installed/x64-linux/lib/libz.a";-sBZIP2_BINARY=bz2;-sBZIP2_LIBPATH="/home/ubuntu/git-third/vcpkg/installed/x64-linux/lib/libbz2.a";variant=release  
--layout=system  
--with-atomic  
--with-random  
--with-date_time  
--with-filesystem  
--with-system  
--with-thread  
--with-chrono  
-sZLIB_INCLUDE=;/home/ubuntu/git-third/vcpkg/installed/x64-linux/include;  
-sBZIP2_INCLUDE=;/home/ubuntu/git-third/vcpkg/installed/x64-linux/include;  
-sICU_PATH=;ICU_PATH-NOTFOUND;  
-j1  
-sBOOST_ROOT=/home/ubuntu/git-third/vcpkg/installed/x64-linux/tools/boost-build  
-sBOOST_BUILD_PATH=/home/ubuntu/git-third/vcpkg/installed/x64-linux/tools/boost-build  
--debug-configuration  
--ignore-site-config  
--hash  
-q  
architecture=x86  
threading=multi  
threadapi=pthread  
debug-symbols=on  
  
And then, some error infos print into log files:  
  
[1/2] cd /home/ubuntu/git-third/vcpkg/buildtrees/boost-context/src/ost-1.67.0-f12b76283b/build && /home/ubuntu/git-third/vcpkg/installed/x64-linux/tools/boost-build/b2 --user-config=/home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel/user-config.jam --stagedir=/home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel/stage --build-dir=/home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel runtime-link=static link=static target-os=linux toolset=gcc -sZLIB_BINARY=z -sZLIB_LIBPATH="/home/ubuntu/git-third/vcpkg/installed/x64-linux/lib/libz.a" -sBZIP2_BINARY=bz2 -sBZIP2_LIBPATH="/home/ubuntu/git-third/vcpkg/installed/x64-linux/lib/libbz2.a" variant=release --layout=system --with-atomic --with-random --with-date_time --with-filesystem --with-system --with-thread --with-chrono -sZLIB_INCLUDE="/home/ubuntu/git-third/vcpkg/installed/x64-linux/include" -sBZIP2_INCLUDE="/home/ubuntu/git-third/vcpkg/installed/x64-linux/include" -sICU_PATH="ICU_PATH-NOTFOUND" -j1 -sBOOST_ROOT=/home/ubuntu/git-third/vcpkg/installed/x64-linux/tools/boost-build -sBOOST_BUILD_PATH=/home/ubuntu/git-third/vcpkg/installed/x64-linux/tools/boost-build --debug-configuration --ignore-site-config --hash -q architecture=x86 threading=multi threadapi=pthread debug-symbols=on  
notice: found boost-build.jam at /home/ubuntu/git-third/vcpkg/installed/x64-linux/tools/boost-build/boost-build.jam  
notice: loading Boost.Build from /home/ubuntu/git-third/vcpkg/installed/x64-linux/tools/boost-build/src/kernel  
notice: Site configuration files will be ignored due to the  
notice: --ignore-site-config command-line option.  
notice: Loading explicitly specified user configuration file:  
    /home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel/user-config.jam  
notice: Searching '/home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel' for user-config configuration file 'user-config.jam'.  
notice: Loading user-config configuration file 'user-config.jam' from '/home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel'.  
notice: will use '/usr/bin/c++' for gcc, condition <toolset>gcc-5.4.1  
notice: using gcc libraries :: <toolset>gcc-5.4.1 :: /usr/bin /usr/lib /usr/lib32 /usr/lib64  
notice: using gcc archiver :: <toolset>gcc-5.4.1 :: /usr/bin/ar  
notice: using gcc ranlib :: <toolset>gcc-5.4.1 :: /usr/bin/ranlib  
warning: toolset gcc initialization: can not find tool windres  
warning: initialized from /home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel/user-config.jam:19  
notice: using rc compiler :: <toolset>gcc-5.4.1 :: /usr/bin/as  
error: No best alternative for ../build/asm_sources  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>qcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>aapcs <address-model>64 <architecture>arm <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>o32 <address-model>32 <architecture>mips1 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>o32 <address-model>32 <architecture>mips1 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>xcoff <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>power <binary-format>xcoff <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>xcoff <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>power <binary-format>xcoff <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32_64 <architecture>power <binary-format>mach-o <threading>multi  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang-win  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>ms <address-model>32 <architecture>x86 <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>darwin  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>64 <architecture>x86 <binary-format>mach-o <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>clang-win  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>ms <address-model>64 <architecture>x86 <binary-format>pe <threading>multi <toolset>msvc  
        not matched  
    next alternative: required properties: <abi>x32 <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>clang  
        not matched  
    next alternative: required properties: <abi>x32 <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>gcc  
        not matched  
    next alternative: required properties: <abi>x32 <address-model>64 <architecture>x86 <binary-format>elf <threading>multi <toolset>intel  
        not matched  
    next alternative: required properties: <abi>sysv <address-model>32_64 <architecture>x86 <binary-format>mach-o <threading>multi  
        not matched  
    next alternative: required properties: <abi>sysv <architecture>combined <binary-format>mach-o <threading>multi  
        not matched  
Performing configuration checks  
  
    - C++11 mutex              : no  
...patience...  
...patience...  
...found 1260 targets...  
...updating 9 targets...  
gcc.compile.c++ /home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel/boost/build/7a16460738b59e5f724909d8c39ba2f7/execution_context.o  
gcc.compile.c++ /home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel/boost/build/7a16460738b59e5f724909d8c39ba2f7/posix/stack_traits.o  
gcc.archive /home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel/boost/build/7a16460738b59e5f724909d8c39ba2f7/libboost_context.a  
common.copy /home/ubuntu/git-third/vcpkg/buildtrees/boost-context/x64-linux-rel/stage/lib/libboost_context.a  
...updated 9 targets...  
  
  
Why not compile asm files?

---

## Comment 3

> Username: yyzybb537  
> Created at: 2018-05-06 02:54:01 UTC  
> Url: https://github.com/boostorg/build/issues/303#issuecomment-386849806  

I'm not understand bjam, and the document has too few info for `alias` keyword.  
So I don't know how to fix this bug.

---

## Comment 4

> Username: ras0219-msft  
> Created at: 2018-05-06 18:32:16 UTC  
> Url: https://github.com/boostorg/build/issues/303#issuecomment-386901837  

This is very likely a vcpkg packaging bug -- we're doing some somewhat-unsupported things to enable modular boost builds. I'll investigate over on our side and report back with results.

---

## Comment 5

> Username: yyzybb537  
> Created at: 2018-05-20 04:59:12 UTC  
> Url: https://github.com/boostorg/build/issues/303#issuecomment-390457975  

I't fixed.
