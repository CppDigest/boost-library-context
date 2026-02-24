# #289 - boost 1.66 - Ubuntu 16.04 - Running b2, g++ gcc cannot find pyconfig.h [Open]

> Username: joseortiz3  
> Created at: 2018-01-28 00:45:50 UTC  
> Updated at: 2021-05-29 18:22:18 UTC  
> Url: https://github.com/boostorg/build/issues/289  

I'm following the [Getting Started](http://www.boost.org/doc/libs/1_66_0/more/getting_started/unix-variants.html) guide for 1.66.  
  
# Versions  
OS: Linux Mint 18.3 ( essentially Ubuntu 16.04 )  
  
Python: Python 2.7, Python 3.6, and Anaconda 5.0.1 w/ Python 3.6  
  
- bootstrap.sh sets Anaconda 5.0.1 as my default python, but this also occurred when 2.7 was my default. I've tried both.  
  
Boost: 1.66  
  
GCC: 5.4.0 (the default for ubuntu 16.04 is 5.3.1 but I needed 5.4.0 for something so now this is my default)  
  
# Behavior  
  
I run ./bootstrap.sh and get the following output which seems normal:  
```  
Building Boost.Build engine with toolset gcc... tools/build/src/engine/bin.linuxx86_64/b2  
Detecting Python version... 3.6  
Detecting Python root... /home/joey/anaconda3  
Unicode/ICU support for Boost.Regex?... not found.  
Backing up existing Boost.Build configuration in project-config.jam.3  
Generating Boost.Build configuration in project-config.jam...  
  
Bootstrapping is done. To build, run:  
  
    ./b2  
```  
I have tried this process with two different default python versions, the default 2.7 with ubuntu and afterwords 3.6 with anaconda. Same problems both times.  
  
I run ./b2 and get pyconfig.h not-found errors:  
  
```  
Performing configuration checks  
  
    - 32-bit                   : no  (cached)  
    - 64-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes (cached)  
    - C++11 mutex              : no  (cached)  
    - lockfree boost::atomic_flag : yes (cached)  
    - Boost.Config Feature Check: cxx11_auto_declarations : no  (cached)  
    - Boost.Config Feature Check: cxx11_constexpr : no  (cached)  
    - Boost.Config Feature Check: cxx11_defaulted_functions : no  (cached)  
    - Boost.Config Feature Check: cxx11_final : no  (cached)  
    - Boost.Config Feature Check: cxx11_hdr_mutex : no  (cached)  
    - Boost.Config Feature Check: cxx11_hdr_regex : no  (cached)  
    - Boost.Config Feature Check: cxx11_hdr_tuple : no  (cached)  
    - Boost.Config Feature Check: cxx11_lambdas : no  (cached)  
    - Boost.Config Feature Check: cxx11_noexcept : no  (cached)  
    - Boost.Config Feature Check: cxx11_nullptr : no  (cached)  
    - Boost.Config Feature Check: cxx11_rvalue_references : no  (cached)  
    - Boost.Config Feature Check: cxx11_template_aliases : no  (cached)  
    - Boost.Config Feature Check: cxx11_thread_local : no  (cached)  
    - Boost.Config Feature Check: cxx11_variadic_templates : no  (cached)  
    - has_icu builds           : no  (cached)  
warning: Graph library does not contain MPI-based parallel components.  
note: to enable them, add "using mpi ;" to your user-config.jam  
    - zlib                     : yes (cached)  
    - bzip2                    : no  (cached)  
    - lzma                     : no  (cached)  
    - iconv (libc)             : yes (cached)  
    - icu                      : no  (cached)  
    - icu (lib64)              : no  (cached)  
    - native-atomic-int32-supported : yes (cached)  
    - native-syslog-supported  : yes (cached)  
    - pthread-supports-robust-mutexes : yes (cached)  
    - compiler-supports-visibility : yes (cached)  
    - compiler-supports-ssse3  : yes (cached)  
    - compiler-supports-avx2   : yes (cached)  
    - gcc visibility           : yes (cached)  
    - long double support      : yes (cached)  
warning: skipping optional Message Passing Interface (MPI) library.  
note: to enable MPI support, add "using mpi ;" to user-config.jam.  
note: to suppress this message, pass "--without-mpi" to bjam.  
note: otherwise, you can safely ignore this message.  
    - libbacktrace builds      : yes (cached)  
    - addr2line builds         : yes (cached)  
    - WinDbg builds            : no  (cached)  
    - WinDbgCached builds      : no  (cached)  
    - zlib                     : yes (cached)  
    - bzip2                    : no  (cached)  
    - lzma                     : no  (cached)  
  
Component configuration:  
  
    - atomic                   : building  
    - chrono                   : building  
    - container                : building  
    - context                  : building  
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
    - mpi                      : building  
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
  
...patience...  
...patience...  
...patience...  
...patience...  
...patience...  
...patience...  
...found 14936 targets...  
...updating 78 targets...  
gcc.compile.c++ bin.v2/libs/python/build/gcc-gnu-5.4.0/release/threadapi-pthread/threading-multi/list.o  
In file included from ./boost/python/detail/prefix.hpp:13:0,  
                 from ./boost/python/list.hpp:8,  
                 from libs/python/src/list.cpp:5:  
./boost/python/detail/wrap_python.hpp:50:23: fatal error: pyconfig.h: No such file or directory  
compilation terminated.  
  
    "g++"   -O3 -finline-functions -Wno-inline -Wall -pthread -fPIC -m64  -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DNDEBUG  -I"." -I"/home/joey/anaconda3/include/python3.6" -c -o "bin.v2/libs/python/build/gcc-gnu-5.4.0/release/threadapi-pthread/threading-multi/list.o" "libs/python/src/list.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/python/build/gcc-gnu-5.4.0/release/threadapi-pthread/threading-multi/list.o...  
gcc.compile.c++ bin.v2/libs/python/build/gcc-gnu-5.4.0/release/threadapi-pthread/threading-multi/long.o  
In file included from ./boost/python/detail/prefix.hpp:13:0,  
                 from ./boost/python/long.hpp:8,  
                 from libs/python/src/long.cpp:5:  
./boost/python/detail/wrap_python.hpp:50:23: fatal error: pyconfig.h: No such file or directory  
compilation terminated.  
  
    "g++"   -O3 -finline-functions -Wno-inline -Wall -pthread -fPIC -m64  -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DNDEBUG  -I"." -I"/home/joey/anaconda3/include/python3.6" -c -o "bin.v2/libs/python/build/gcc-gnu-5.4.0/release/threadapi-pthread/threading-multi/long.o" "libs/python/src/long.cpp"  
  
...failed gcc.compile.c++ bin.v2/libs/python/build/gcc-gnu-5.4.0/release/threadapi-pthread/threading-multi/long.o...  
gcc.compile.c++ bin.v2/libs/python/build/gcc-gnu-5.4.0/release/threadapi-pthread/threading-multi/dict.o  
In file included from ./boost/python/detail/prefix.hpp:13:0,  
                 from ./boost/python/dict.hpp:8,  
                 from libs/python/src/dict.cpp:4:  
./boost/python/detail/wrap_python.hpp:50:23: fatal error: pyconfig.h: No such file or directory  
compilation terminated.  
```  
(and 66 failed python-related targets later b2 finishes)  
```  
...failed updating 66 targets...  
...skipped 12 targets..  
```  
I'm not really sure where to go from here - if this is an issue others face when trying to build boost on ubuntu 14-16, or if my system configuration is just messed up?  
  
Hope to find the cause, and thank you for any info / suggestions you can provide.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-01-28 01:08:15 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-361029645  

AMDG  
  
On 01/27/2018 05:45 PM, Jose Ortiz wrote:  
>   
> gcc.compile.c++ bin.v2/libs/python/build/gcc-gnu-5.4.0/release/threadapi-pthread/threading-multi/list.o  
> In file included from ./boost/python/detail/prefix.hpp:13:0,  
>                  from ./boost/python/list.hpp:8,  
>                  from libs/python/src/list.cpp:5:  
> ./boost/python/detail/wrap_python.hpp:50:23: fatal error: pyconfig.h: No such file or directory  
> compilation terminated.  
> <snip>   
> I'm not really sure where to go from here - if this is an issue others face when trying to build boost on ubuntu 14-16, or if my system configuration is just messed up?  
>   
> Hope to find the cause, and thank you for any info / suggestions you can provide.  
>   
  
Boost.Build can sometimes fail to find the correct paths  
automatically.  See https://svn.boost.org/trac10/ticket/6826 and  
https://github.com/boostorg/build/pull/250  
  
As a workaround, try setting the paths manually in  
project-config.jam or user-config.jam:  
  
using python  
  : # version  
  : # cmd  
  : # include-path  
  : # library-path  
  ;  
  
See  
$ b2 --help python.init  
for more details.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: joseortiz3  
> Created at: 2018-01-28 01:49:16 UTC  
> Updated at: 2018-01-28 01:58:30 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-361031335  

@swatanabe    
Thanks for your feedback. I'm a little confused with the bjam syntax.  
  
Which path should `include-path` be set to? Or is there a way of specifying both of these?  
  
- /home/joey/anaconda3/include/python3.6m          <-- _this is where `pyconfig.h` actually is_  
- /home/joey/anaconda3/include  
- or something else?  
  
Also the `library-path`: should it be this?   
- /home/joey/anaconda3/lib  
- /home/joey/anaconda3/bin  
- or something else?  
  
Also, I noticed I have the /python3.6m directory that ends in an 'm', which according to the link you provided causes problems. Hopefully https://github.com/boostorg/build/pull/250 fixes this?

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-01-28 02:11:02 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-361032309  

AMDG  
  
On 01/27/2018 06:49 PM, Jose Ortiz wrote:  
> Thanks for your feedback. I'm a little confused with the bjam syntax.  
>   
> Which path should `include-path` be set to? Or is there a way of specifying both of these?  
>   
> - /home/joey/anaconda3/include/python3.6m> - /home/joey/anaconda3/include  
>   
  
  The first, I presume.  Boost.Build will  
pass exactly the paths you give to the  
compiler and linker: -I$(includes) -L$(libraries)  
  
> Also the `library-path`: should it be this?   
> - /home/joey/anaconda3/lib  
> or /bin, or something else?  
>   
  
  Is that where python3.6.so lives?  Actually,  
poking around in the code, it looks like just  
setting the version to 3.6m may be what you need.  
  
using python : 3.6m : /home/joey/anaconda3 ;  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: joseortiz3  
> Created at: 2018-01-28 02:16:59 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-361032567  

You're right! Works now. I used  
  
```  
if ! [ python.configured ]  
{  
    using python : 3.6 : /home/joey/anaconda3 : /home/joey/anaconda3/include/python3.6m ;  
}  
```  
Thanks a lot for your help!

---

## Comment 5

> Username: fbenda  
> Created at: 2018-04-06 16:00:10 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-379298553  

I think I have the same issue. My project-config.jam file:   
  
# Boost.Build Configuration  
# Automatically generated by bootstrap.sh  
  
import option ;  
import feature ;  
  
# Compiler configuration. This definition will be used unless  
# you already have defined some toolsets in your user-config.jam  
# file.  
if ! gcc in [ feature.values <toolset> ]  
{  
    using gcc ;   
}  
  
project : default-build <toolset>gcc ;  
  
# Python configuration   
using python : 3.6 : /usr/include/x86_64-linux-gnu/python3.6m : /home/bendafe/.virtualenvs/cv ;  
  
# List of --with-<library> and --without-<library>  
# options. If left empty, all libraries will be built.  
# Options specified on the command line completely  
# override this variable.  
libraries =  --with-python ;  
  
# These settings are equivivalent to corresponding command-line  
# options.  
option.set prefix : /usr/local ;  
option.set exec-prefix : /usr/local ;  
option.set libdir : /usr/local/lib ;  
option.set includedir : /usr/local/include ;  
  
# Stop on first error  
option.set keep-going : false ;  
  
my ./b2 output:   
  
Building the Boost C++ Libraries.  
  
  
error: No best alternative for /python_for_extensions  
    next alternative: required properties: <python>3.6 <target-os>linux  
        matched  
    next alternative: required properties: <python>3.6 <target-os>linux  
        matched  
error: No best alternative for /python_for_extensions  
    next alternative: required properties: <python>3.6 <target-os>linux  
        matched  
    next alternative: required properties: <python>3.6 <target-os>linux  
        matched  
error: No best alternative for /python_for_extensions  
    next alternative: required properties: <python>3.6 <target-os>linux  
        matched  
    next alternative: required properties: <python>3.6 <target-os>linux  
        matched  
error: No best alternative for /python_for_extensions  
    next alternative: required properties: <python>3.6 <target-os>linux  
        matched  
    next alternative: required properties: <python>3.6 <target-os>linux  
        matched  
  
Component configuration:  
  
    - chrono                   : not building  
    - date_time                : not building  
    - exception                : not building  
    - filesystem               : not building  
    - graph                    : not building  
    - graph_parallel           : not building  
    - iostreams                : not building  
    - locale                   : not building  
    - math                     : not building  
    - mpi                      : not building  
    - program_options          : not building  
    - python                   : building  
    - random                   : not building  
    - regex                    : not building  
    - serialization            : not building  
    - signals                  : not building  
    - system                   : not building  
    - test                     : not building  
    - thread                   : not building  
    - timer                    : not building  
    - wave                     : not building  
  
...patience...  
...patience...  
...found 1531 targets...  
...updating 68 targets...  
gcc.compile.c++ bin.v2/libs/python/build/gcc-5.4.1/release/threading-multi/numeric.o  
In file included from ./boost/python/detail/prefix.hpp:13:0,  
                 from ./boost/python/numeric.hpp:8,  
                 from libs/python/src/numeric.cpp:6:  
./boost/python/detail/wrap_python.hpp:50:23: fatal error: pyconfig.h: No such file or directory  
compilation terminated.  
[....]  
...failed updating 56 targets...  
...skipped 12 targets...  
  
I have the pyconfig.h under /usr/include/x86_64-linux-gnu/python3.6m/pyconfig.h and my python root is in the virtualenv folder.   
What can be the problem?

---

## Comment 6

> Username: ATPs  
> Created at: 2018-07-23 03:01:14 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-406925734  

`ln -s /home/p/anaconda/anaconda3_5.2.0/include/python3.6m/ /home/p/anaconda/anaconda3_5.2.0/include/python3.6 `   
solved my problem. The program is trying to find the file in the folder "./include/python3.6". However, the default name is "./include/python3.6m" in Anaconda

---

## Comment 7

> Username: rickystream94  
> Created at: 2018-09-25 01:04:48 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-424173517  

I know this is quite old, but I encountered the same issue with Boost 1.68. I'm using anaconda2 with python 3.6.  
I first found my `pyconfig.h` file in `/home/XXX/anaconda2/envs/py36/include/python3.6m`  
I'm not sure whether all changes below are actually needed, but it's the configuration that helped me solve it:  
`project-config.jam` (under python config section):  
```  
if ! [ python.configured ]  
{  
    using python  
    : 3.6  
    : /home/XXX/anaconda2/envs/py36  
    : /home/XXX/anaconda2/envs/py36/include  
    : /home/XXX/anaconda2/envs/py36/lib  
    ;  
}  
```  
Then I launched from terminal:  
```  
python_root=`python -c "import sys; print(sys.prefix)"`  
export CPLUS_INCLUDE_PATH="$CPLUS_INCLUDE_PATH:/home/XXX/anaconda2/envs/py36/include/python3.6m"  
./bootstrap.sh --prefix=$PREFIX --with-python-root=$python_root  
./b2 install  
```

---

## Comment 8

> Username: chris1248  
> Created at: 2018-11-27 22:40:45 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-442245780  

I have encountered this problem too.  
The boost build system really is a P.O.S.

---

## Comment 9

> Username: mtrenkmann  
> Created at: 2018-12-10 14:20:22 UTC  
> Updated at: 2019-04-15 07:54:55 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-445831282  

Here is how I built Boost 1.66 with Python 3 on Debian Stretch **successfully**.  
  
```sh  
sudo apt install git python3 python3-dev  
git clone https://github.com/boostorg/boost.git  
cd boost  
git checkout tags/boost-1.66.0  
git submodule update --init  
./bootstrap.sh --with-python=python3  
./b2 --build-dir=build --layout=versioned cxxstd=14 install  
```  
  
The important part is `./bootstrap.sh --with-python=python3`. Arguments to `./b2` may vary.

---

## Comment 10

> Username: yoonghm  
> Created at: 2019-03-29 01:03:35 UTC  
> Updated at: 2019-03-29 01:06:02 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-477825831  

We could use `python-config --includes` to print out include files come with a particular version of Python.  However some Python distributions (such as Anaconda) do not come with `python-config`.  The best way is to use python binary to display its include path.  
  
```python  
from sysconfig import get_paths as gp  
print(gp()['include'])  
```  
  
We can run the code via command line:  
  
```bash  
python -c "from sysconfig import get_paths as gp; print(gp()['include'])"  
```

---

## Comment 11

> Username: fantauzzi  
> Created at: 2019-07-27 20:51:41 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-515712785  

In my case the build didn't find the include for Python 3 in a virtualenv. I have edited the boost_1_70_0/project-config.jam similarly to @joseortiz3, as per @swatanabe indications, which resolved the issue:  
  
```  
if ! [ python.configured ]  
{  
    using python : 3.6 : /home/fanta/.local/virtualenv/python3.6 : /home/fanta/.local/virtualenv/python3.6/include/python3.6m ;  
}  
```

---

## Comment 12

> Username: yoonghm  
> Created at: 2019-08-19 15:52:57 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-522638966  

I have submitted a pull-request to fix the issue at [321](https://github.com/boostorg/boost/pull/321)

---

## Comment 13

> Username: stale[bot]  
> Created at: 2021-05-29 18:21:49 UTC  
> Url: https://github.com/boostorg/build/issues/289#issuecomment-850877269  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
