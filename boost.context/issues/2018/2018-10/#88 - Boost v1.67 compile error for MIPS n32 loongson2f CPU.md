# #88 - Boost v1.67 compile error for MIPS n32 loongson2f CPU [Closed]

> Username: emtone  
> Created at: 2018-10-28 06:09:04 UTC  
> Updated at: 2021-07-13 19:07:26 UTC  
> Closed at: 2021-07-13 19:07:25 UTC  
> Url: https://github.com/boostorg/context/issues/88  

Hello,  
I use gentoo on Lemote Yeeloong 8101 notebook which has a MIPS n32 loongson2f CPU. It's OK when I compile without "context" USE flag. Now I want to use KiCad on this computer which need boost recompile with "contest" USE flag:  
`[ebuild U *] dev-libs/boost-1.67.0:0/1.67.0::gentoo [1.65.0:0/1.65.0::gentoo] USE="context* nls python threads -debug -doc -icu (-mpi) -static-libs -tools" ABI_MIPS="(n32) -n64 -o32" PYTHON_TARGETS="python2_7 python3_6 -python3_4 -python3_5 -python3_7%" 0 KiB`  
When I compile with "context" USE flag, An error has occurred:  
```  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes  
error: No best alternative for libs/context/build/asm_sources  
    next alternative: required properties: <abi>aapcs <address-model>32 <architecture>arm <binary-format>elf <threading>multi <toolset>clang  
        not matched  
```  
```  
warning: Graph library does not contain MPI-based parallel components.  
note: to enable them, add "using mpi ;" to your user-config.jam  
    - zlib                     : yes  
    - bzip2                    : yes  
    - lzma                     : yes  
    - iconv (libc)             : yes  
    - native-atomic-int32-supported : yes  
    - native-syslog-supported  : yes  
    - pthread-supports-robust-mutexes : yes  
    - compiler-supports-visibility : yes  
    - x86                      : no  
    - arm                      : no  
    - mips1                    : no  
    - power                    : no  
    - sparc                    : no  
    - gcc visibility           : yes  
    - long double support      : yes  
    - libbacktrace builds      : no  
    - addr2line builds         : yes  
    - WinDbg builds            : no  
    - WinDbgCached builds      : no  
    - BOOST_COMP_GNUC >= 4.3.0 : yes  
```  
```  
 "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/fiber/build/gcc-8.2/gentoorelease/pch-off/threading-multi/algo/work_stealing.o" "libs/fiber/src/algo/work_stealing.cpp"  
  
{standard input}: Assembler messages:  
{standard input}:1475: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
{standard input}:1545: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
{standard input}:1552: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
{standard input}:2254: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
{standard input}:2327: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
...failed updating 1 target...  
```

---

## Comment 1

> Username: olk  
> Created at: 2018-10-28 09:32:12 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-433689624  

ABI N32 is not supported, only O32 - but you are welcome to provide a patch (I've no access to a MIPS/N32 system)

---

## Comment 2

> Username: emtone  
> Created at: 2018-10-28 11:25:54 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-433696477  

I want to provide a patch, but I don't know How to do. I can provide any information you need.

---

## Comment 3

> Username: olk  
> Created at: 2018-10-29 06:59:59 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-433806638  

- fork boost.context  
- create asm-files for N32  
- adjust build/Jamfile (adding N32 to property <abi> + add selection of sources)  
- push commit  
- on github.com create an pull-request

---

## Comment 4

> Username: emtone  
> Created at: 2018-10-30 15:49:33 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-434356148  

Thank you, I will try.

---

## Comment 5

> Username: emtone  
> Created at: 2018-10-30 16:52:45 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-434382202  

I download the context-develop.zip file from https://github.com/emtone/context.git and unzip it. Try to compile use bjam:  
```  
gentoo@yeeloong ~/context-develop/build $ ls  
Jamfile.v2  architecture.jam  cxx11_hdr_mutex.cpp  
gentoo@yeeloong ~/context-develop/build $ pwd  
/home/gentoo/context-develop/build  
gentoo@yeeloong ~/context-develop/build $ bjam  
/usr/share/boost-build/build/project.jam:111: in load-parent from module project  
error: Could not find parent for project at '.'  
error: Did not find Jamfile.jam or Jamroot.jam in any parent directory.  
/usr/share/boost-build/build/project.jam:464: in initialize from module project  
/usr/share/boost-build/build/project.jam:306: in load-jamfile from module project  
/usr/share/boost-build/build/project.jam:64: in load from module project  
/usr/share/boost-build/build/project.jam:145: in project.find from module project  
/usr/share/boost-build/build-system.jam:618: in load from module build-system  
/usr/share/boost-build/kernel/modules.jam:295: in import from module modules  
/usr/share/boost-build/kernel/bootstrap.jam:139: in boost-build from module  
/usr/share/boost-build/boost-build.jam:8: in module scope from module  
```  
Is there some thing wrong?

---

## Comment 6

> Username: olk  
> Created at: 2018-10-30 17:48:25 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-434403116  

you need the complete git repo (boost.context depends on other libraries as well as on boost.build)

---

## Comment 7

> Username: emtone  
> Created at: 2018-10-31 02:04:40 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-434535052  

OK, I will follow this Boost.Build Tutorial: https://boostorg.github.io/build/tutorial.html

---

## Comment 8

> Username: emtone  
> Created at: 2018-10-31 16:27:18 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-434754570  

I use gentoo ebuild env to compile Boost:  
```  
yeeloong /var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32 # pwd  
/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32  
yeeloong /var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32 # ls  
INSTALL  LICENSE_1_0.txt  boost            boost.css  boostcpp.jam   bootstrap.sh  index.htm   libs  rst.css  status  user-config.jam  
Jamroot  bin.v2           boost-build.jam  boost.png  bootstrap.bat  doc           index.html  more  stage    tools  
yeeloong /var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32 # b2 --user-config=/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/user-config.jam gentoorelease -j1 -q -d+2 pch=off --disable-icu boost.locale.icu=off --without-mpi --boost-build=/usr/share/boost-build --prefix=/var/tmp/portage/dev-libs/boost-1.67.0/image/usr --layout=system threading=multi link=shared --python-buildid=2.7  
/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : none (cached)  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes (cached)  
error: No best alternative for libs/context/build/asm_sources  
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
    - C++11 mutex              : yes (cached)  
    - lockfree boost::atomic_flag : yes (cached)  
    - Boost.Config Feature Check: cxx11_final : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_mutex : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_regex : yes (cached)  
    - Boost.Config Feature Check: cxx11_hdr_tuple : yes (cached)  
    - Boost.Config Feature Check: cxx11_lambdas : yes (cached)  
    - Boost.Config Feature Check: cxx11_noexcept : yes (cached)  
    - Boost.Config Feature Check: cxx11_nullptr : yes (cached)  
    - Boost.Config Feature Check: cxx11_rvalue_references : yes (cached)  
    - Boost.Config Feature Check: cxx11_template_aliases : yes (cached)  
    - Boost.Config Feature Check: cxx11_thread_local : yes (cached)  
    - Boost.Config Feature Check: cxx11_variadic_templates : yes (cached)  
    - Boost.Config Feature Check: cxx11_auto_declarations : yes (cached)  
    - Boost.Config Feature Check: cxx11_constexpr : yes (cached)  
    - Boost.Config Feature Check: cxx11_defaulted_functions : yes (cached)  
    - has_icu builds           : no  (cached)  
warning: Graph library does not contain MPI-based parallel components.  
note: to enable them, add "using mpi ;" to your user-config.jam  
    - zlib                     : yes (cached)  
    - bzip2                    : yes (cached)  
    - lzma                     : yes (cached)  
    - iconv (libc)             : yes (cached)  
    - native-atomic-int32-supported : yes (cached)  
    - native-syslog-supported  : yes (cached)  
    - pthread-supports-robust-mutexes : yes (cached)  
    - compiler-supports-visibility : yes (cached)  
    - x86                      : no  (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - gcc visibility           : yes (cached)  
    - long double support      : yes (cached)  
    - libbacktrace builds      : no  (cached)  
    - addr2line builds         : yes (cached)  
    - WinDbg builds            : no  (cached)  
    - WinDbgCached builds      : no  (cached)  
    - BOOST_COMP_GNUC >= 4.3.0 : yes (cached)  
  
Component configuration:  
  
    - atomic                   : building  
    - chrono                   : building  
    - container                : building  
    - context                  : building  
    - contract                 : building  
    - coroutine                : building  
    - date_time                : building  
    - exception                : building  
    - fiber                    : building  
    - filesystem               : building  
    - graph                    : building  
    - graph_parallel           : building  
    - iostreams                : building  
    - locale                   : building  
    - log                      : building  
    - math                     : building  
    - mpi                      : not building  
    - program_options          : building  
    - python                   : building  
    - random                   : building  
    - regex                    : building  
    - serialization            : building  
    - signals                  : building  
    - stacktrace               : building  
    - system                   : building  
    - test                     : building  
    - thread                   : building  
    - timer                    : building  
    - type_erasure             : building  
    - wave                     : building  
```

---

## Comment 9

> Username: emtone  
> Created at: 2018-10-31 16:32:02 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-434756384  

b2 --user-config=/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/user-config.jam gentoorelease -j1 -q -d+2 pch=off --disable-icu boost.locale.icu=off --without-mpi --boost-build=/usr/share/boost-build --prefix=/var/tmp/portage/dev-libs/boost-1.67.0/image/usr --layout=system threading=multi link=shared --python-buildid=2.7  
/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
  
The user-config.jam:  
```  
yeeloong /var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32 # cat user-config.jam   
using gcc : 8.2 : mips64el-unknown-linux-gnu-g++ : <cflags>"-O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe" <cxxflags>"-O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14" <linkflags>"-Wl,-O1 -Wl,--as-needed" ;  
  
using python : 2.7 : /usr/bin/python2.7 : /usr/include/python2.7 : /var/tmp/portage/dev-libs/boost-1.67.0/temp ;  
  
```

---

## Comment 10

> Username: olk  
> Created at: 2018-10-31 19:08:33 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-434811056  

cross compiling will not work!  
  
- default architecture     : none (cached)  
  
should be  
- default architecture     : mips  
  
try to add _architecture=mips_ at b2 command line

---

## Comment 11

> Username: emtone  
> Created at: 2018-11-01 14:27:36 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-435057477  

I am compiling on Lemote Yeeloong 8101 not cross compiling.  
Add _architecture=mips_ at b2 command line:  
```  
yeeloong /var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32 # b2 --user-config=/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/user-config.jam gentoorelease -j1 -q -d+2 pch=off --disable-icu boost.locale.icu=off --without-mpi --boost-build=/usr/share/boost-build --prefix=/var/tmp/portage/dev-libs/boost-1.67.0/image/usr --layout=system threading=multi link=shared --python-buildid=2.7 architecture=mips   
/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
/usr/share/boost-build/build/feature.jam:491: in feature.validate-value-string from module feature  
error: "mips" is not a known value of feature <architecture>  
error: legal values: "x86" "ia64" "sparc" "power" "mips1" "mips2" "mips3" "mips4" "mips32" "mips32r2" "mips64" "parisc" "arm" "combined" "combined-x86-power"  
/usr/share/boost-build/build/property.jam:341: in validate1 from module property  
/usr/share/boost-build/build/property.jam:367: in property.validate from module property  
/usr/share/boost-build/build/build-request.jam:271: in convert-command-line-element from module build-request  
/usr/share/boost-build/build/build-request.jam:222: in build-request.convert-command-line-elements from module build-request  
/usr/share/boost-build/build-system.jam:774: in load from module build-system  
/usr/share/boost-build/kernel/modules.jam:295: in import from module modules  
/usr/share/boost-build/kernel/bootstrap.jam:139: in boost-build from module  
/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/boost-build.jam:17: in module scope from module  
```  
Add _architecture=mips3_ at b2 command line is the same without _architecture_ parameter:  
```  
yeeloong /var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32 # b2 --user-config=/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/user-config.jam gentoorelease -j1 -q -d+2 pch=off --disable-icu boost.locale.icu=off --without-mpi --boost-build=/usr/share/boost-build --prefix=/var/tmp/portage/dev-libs/boost-1.67.0/image/usr --layout=system threading=multi link=shared --python-buildid=2.7 architecture=mips3  
/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
Performing configuration checks  
  
    - default address-model    : 32-bit (cached)  
    - default architecture     : none (cached)  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes (cached)  
...  
 "mips64el-unknown-linux-gnu-g++"   -O2 -march=loongson2f -Wa,-mfix-loongson2f-nop -pipe -std=c++14 -fPIC -pthread -finline-functions -Wno-inline -Wall  -DBOOST_ALL_NO_LIB=1 -DBOOST_CONTEXT_DYN_LINK=1 -DBOOST_FIBERS_DYN_LINK=1 -DBOOST_FIBERS_SOURCE -DBOOST_FILESYSTEM_DYN_LINK=1 -DBOOST_SYSTEM_DYN_LINK=1 -DNDEBUG  -I"." -c -o "bin.v2/libs/fiber/build/gcc-8.2/gentoorelease/architecture-mips3/pch-off/threading-multi/algo/work_stealing.o" "libs/fiber/src/algo/work_stealing.cpp"  
  
{standard input}: Assembler messages:  
{standard input}:1475: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
{standard input}:1545: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
{standard input}:1552: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
{standard input}:2254: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
{standard input}:2327: Error: opcode not supported on this processor: loongson2f (mips3) `pause'  
...failed updating 1 target...  
```  
I think may be the check program can't correct check the 8101 notebook's architecture, Is this an error info?  
_**/var/tmp/portage/dev-libs/boost-1.67.0/work/boost_1_67_0-abi_mips_n32.n32/libs/predef/check/../tools/check/predef.jam:46: Unescaped special character in argument $(language)::$(expression)**_

---

## Comment 12

> Username: olk  
> Created at: 2018-11-01 14:57:20 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-435067211  

- select the correct architecture out of "mips1" "mips2" "mips3" "mips4" "mips32" "mips32r2" "mips64"  
- build/Jamfile add entry for gcc + the selected architecture -> reference new asm files for N32  
- create asm files for N32

---

## Comment 13

> Username: emtone  
> Created at: 2018-11-01 15:07:31 UTC  
> Updated at: 2018-11-07 14:34:50 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-435070799  

What the assembler files used for? I have the loongson2F user guide, but I don't known how to use it for these 3 assembler file.

---

## Comment 14

> Username: emtone  
> Created at: 2018-11-08 01:33:09 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-436840999  

I find the [MIPS-N32-ABI-Handbook](https://www.linux-mips.org/pub/linux/mips/doc/ABI/MIPS-N32-ABI-Handbook.pdf)

---

## Comment 15

> Username: emtone  
> Created at: 2018-11-12 14:47:06 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-437907685  

_“There are eight callee-saved floating point registers, $f24..$f31 for the 64-bit  
interface. There are six for the n32 ABI, the six even registers in $f20..$f30. [The  
o32-bit ABI specifies the six even registers, or even/odd pairs, $f20..$f31.]”_  
  
```  
/*******************************************************  
 *                                                     *  
 *  -------------------------------------------------  *  
 *  |  0  |  1  |  2  |  3  |  4  |  5  |  6  |  7  |  *  
 *  -------------------------------------------------  *  
 *  |  0  |  4  |  8  |  12 |  16 |  20 |  24 |  28 |  *  
 *  -------------------------------------------------  *  
 *  |    F20    |    F22    |    F24    |    F26    |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  8  |  9  |  10 |  11 |  12 |  13 |  14 |  15 |  *  
 *  -------------------------------------------------  *  
 *  |  32 |  36 |  40 |  44 |  48 |  52 |  56 |  60 |  *  
 *  -------------------------------------------------  *  
 *  |    F28    |    F30    |  S0 |  S1 |  S2 |  S3 |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  16 |  17 |  18 |  19 |  20 |  21 |  22 |  23 |  *  
 *  -------------------------------------------------  *  
 *  |  64 |  68 |  72 |  76 |  80 |  84 |  88 |  92 |  *  
 *  -------------------------------------------------  *  
 *  |  S4 |  S5 |  S6 |  S7 |  FP |hiddn|  RA |  PC |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  24 |  25 |  26 |  27 |  28 |  29 |  30 |  31 |  *  
 *  -------------------------------------------------  *  
 *  |  96 | 100 | 104 | 108 | 112 | 116 | 120 | 124 |  *  
 *  -------------------------------------------------  *  
 *  |       ABI ARGS        |  GP | FCTX| DATA|     |  *  
 *  -------------------------------------------------  *  
 *                                                     *  
 * *****************************************************/  
```  
 Is this the reason save start from F20 to F30?

---

## Comment 16

> Username: emtone  
> Created at: 2018-11-24 13:57:24 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-441369688  

I read the "MIPS-N32-ABI-Handbook" again, and think only the Callee-saved register need be saved.  
  
> Note that  
> “Caller-saved” means only that the caller may not assume that the value in the register  
> is preserved across the call.

---

## Comment 17

> Username: emtone  
> Created at: 2018-11-24 14:03:36 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-441370133  

So I think I should process s0..s7, gp, sp, s8, $f20..$f31 even.

---

## Comment 18

> Username: olk  
> Created at: 2018-11-24 18:30:58 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-441386845  

fine, could you provide a pull request, please?

---

## Comment 19

> Username: emtone  
> Created at: 2018-11-25 02:47:28 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-441411084  

OK, I will do.

---

## Comment 20

> Username: emtone  
> Created at: 2018-11-25 03:11:43 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-441411999  

This is my N32 register map, is this right?  
```  
/*******************************************************  
 *                                                     *  
 *  -------------------------------------------------  *  
 *  |  0  |  1  |  2  |  3  |  4  |  5  |  6  |  7  |  *  
 *  -------------------------------------------------  *  
 *  |  0  |  4  |  8  |  12 |  16 |  20 |  24 |  28 |  *  
 *  -------------------------------------------------  *  
 *  |    F20    |    F22    |    F24    |    F26    |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  8  |  9  |  10 |  11 |  12 |  13 |  14 |  15 |  *  
 *  -------------------------------------------------  *  
 *  |  32 |  36 |  40 |  44 |  48 |  52 |  56 |  60 |  *  
 *  -------------------------------------------------  *  
 *  |    F28    |    F30    |     S0    |     S1    |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  16 |  17 |  18 |  19 |  20 |  21 |  22 |  23 |  *  
 *  -------------------------------------------------  *  
 *  |  64 |  68 |  72 |  76 |  80 |  84 |  88 |  92 |  *  
 *  -------------------------------------------------  *  
 *  |     S2    |     S3    |     S4    |     S5    |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  24 |  25 |  26 |  27 |  28 |  29 |  30 |  31 |  *  
 *  -------------------------------------------------  *  
 *  |  96 | 100 | 104 | 108 | 112 | 116 | 120 | 124 |  *  
 *  -------------------------------------------------  *  
 *  |     S6    |     S7    |     FP    |     GP    |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  32 |  33 |  34 |  35 |  36 |  37 |  38 |  39 |  *  
 *  -------------------------------------------------  *  
 *  | 128 | 132 | 136 | 140 | 144 | 148 | 152 | 156 |  *  
 *  -------------------------------------------------  *  
 *  |   hiddn   |     RA    |     PC    |    FCTX   |  *  
 *  -------------------------------------------------  *  
 *  -------------------------------------------------  *  
 *  |  40 |  41 |  42 |  43 |  44 |  45 |  46 |  47 |  *  
 *  -------------------------------------------------  *  
 *  | 160 | 164 | 168 | 172 | 176 | 180 | 184 | 188 |  *  
 *  -------------------------------------------------  *  
 *  |    DATA   |                                   |  *  
 *  -------------------------------------------------  *  
 *                                                     *  
 * *****************************************************/  
```

---

## Comment 21

> Username: olk  
> Created at: 2018-11-25 08:49:53 UTC  
> Updated at: 2018-11-25 08:53:06 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-441424933  

I don't know - I thought you have access to N32 systems and you provide the implementation?!  
  
Did you run the unit_tests (in sub-directory `test` execute b2)?

---

## Comment 22

> Username: olk  
> Created at: 2021-07-13 19:07:25 UTC  
> Url: https://github.com/boostorg/context/issues/88#issuecomment-879330689  

closed because of no progress/no assembler provided.
