# #194 - Broken build boost_python in release 1.64.0 [Closed]

> Username: andrejnau  
> Created at: 2017-04-25 16:07:14 UTC  
> Updated at: 2019-06-14 18:43:39 UTC  
> Closed at: 2019-06-14 18:43:39 UTC  
> Url: https://github.com/boostorg/build/issues/194  

I'm trying to build boost using Visual Studio 2015. This is the output what I got:  
```  
Microsoft Windows [Version 10.0.15063]  
(c) 2017 Microsoft Corporation. All rights reserved.  
  
C:\boost_1_64_0>bootstrap.bat  
Building Boost.Build engine  
  
Bootstrapping is done. To build, run:  
  
    .\b2  
  
To adjust configuration, edit 'project-config.jam'.  
Further information:  
  
    - Command line help:  
    .\b2 --help  
  
    - Getting started guide:  
    http://boost.org/more/getting_started/windows.html  
  
    - Boost.Build documentation:  
    http://www.boost.org/build/doc/html/index.html  
  
C:\boost_1_64_0>b2 --with-python  
Performing configuration checks  
  
    - 32-bit                   : yes  
    - arm                      : no  
    - mips1                    : no  
    - power                    : no  
    - sparc                    : no  
    - x86                      : yes  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes  
  
Component configuration:  
  
    - atomic                   : not building  
    - chrono                   : not building  
    - container                : not building  
    - context                  : not building  
    - coroutine                : not building  
    - coroutine2               : not building  
    - date_time                : not building  
    - exception                : not building  
    - fiber                    : not building  
    - filesystem               : not building  
    - graph                    : not building  
    - graph_parallel           : not building  
    - iostreams                : not building  
    - locale                   : not building  
    - log                      : not building  
    - math                     : not building  
    - metaparse                : not building  
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
    - type_erasure             : not building  
    - wave                     : not building  
  
...found 1 target...  
  
  
The Boost C++ Libraries were successfully built!  
  
The following directory should be added to compiler include paths:  
  
    C:\boost_1_64_0  
  
The following directory should be added to linker library paths:  
  
    C:\boost_1_64_0\stage\lib  
```  
But the build infrastructure didn't even create resulting folder for _boost_python_. The library seems to be skipped.  
In release 1.63.0 the build procedure works fine for me.

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-04-25 16:32:45 UTC  
> Url: https://github.com/boostorg/build/issues/194#issuecomment-297087996  

AMDG  
  
On 04/25/2017 10:07 AM, Andrew Naumov wrote:  
> I'm trying to build boost using Visual Studio 2015. This is the output what I got:  
> ```  
>   
> <snip>  
> C:\boost_1_64_0>b2 --with-python  
> Performing configuration checks  
> <snip>  
>   
> ...found 1 target...  
>   
> ```  
> But the build infrastructure didn't even create resulting folder for _boost_python_. The library seems to be skipped.  
> In release 1.63.0 the build procedure works fine for me.  
>   
  
  Please run b2 with --debug-configuration to show  
more information about the Python configuration.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: andrejnau  
> Created at: 2017-04-25 16:42:10 UTC  
> Url: https://github.com/boostorg/build/issues/194#issuecomment-297090685  

```  
C:\boost_1_64_0>b2 --with-python --debug-configuration  
notice: found boost-build.jam at C:/boost_1_64_0/boost-build.jam  
notice: loading Boost.Build from C:/boost_1_64_0/tools/build/src  
notice: Searching 'C:\WINDOWS' 'C:\Users\andrejnau' 'C:\Users\andrejnau' 'C:\boost_1_64_0\tools/build/src' 'C:/boost_1_64_0/tools/build/src/kernel' 'C:/boost_1_64_0/tools/build/src/util' 'C:/boost_1_64_0/tools/build/src/build' 'C:/boost_1_64_0/tools/build/src/tools' 'C:/boost_1_64_0/tools/build/src/contrib' 'C:/boost_1_64_0/tools/build/src/.' for site-config configuration file 'site-config.jam'.  
notice: Configuration file 'site-config.jam' not found in 'C:\WINDOWS' 'C:\Users\andrejnau' 'C:\Users\andrejnau' 'C:\boost_1_64_0\tools/build/src' 'C:/boost_1_64_0/tools/build/src/kernel' 'C:/boost_1_64_0/tools/build/src/util' 'C:/boost_1_64_0/tools/build/src/build' 'C:/boost_1_64_0/tools/build/src/tools' 'C:/boost_1_64_0/tools/build/src/contrib' 'C:/boost_1_64_0/tools/build/src/.'.  
notice: Searching 'C:\Users\andrejnau' 'C:\Users\andrejnau' 'C:\boost_1_64_0\tools/build/src' 'C:/boost_1_64_0/tools/build/src/kernel' 'C:/boost_1_64_0/tools/build/src/util' 'C:/boost_1_64_0/tools/build/src/build' 'C:/boost_1_64_0/tools/build/src/tools' 'C:/boost_1_64_0/tools/build/src/contrib' 'C:/boost_1_64_0/tools/build/src/.' for user-config configuration file 'user-config.jam'.  
notice: Configuration file 'user-config.jam' not found in 'C:\Users\andrejnau' 'C:\Users\andrejnau' 'C:\boost_1_64_0\tools/build/src' 'C:/boost_1_64_0/tools/build/src/kernel' 'C:/boost_1_64_0/tools/build/src/util' 'C:/boost_1_64_0/tools/build/src/build' 'C:/boost_1_64_0/tools/build/src/tools' 'C:/boost_1_64_0/tools/build/src/contrib' 'C:/boost_1_64_0/tools/build/src/.'.  
notice: Searching '.' for project-config configuration file 'project-config.jam'.  
notice: Loading project-config configuration file 'project-config.jam' from '.'.  
notice: [msvc-cfg] msvc-14.0 detected, command: 'C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\cl.exe'  
notice: [msvc-cfg] msvc-12.0 detected, command: 'C:\Program Files (x86)\Microsoft Visual Studio 12.0\VC\bin\cl.exe'  
notice: [msvc-cfg] msvc-11.0 detected, command: 'C:\Program Files (x86)\Microsoft Visual Studio 11.0\VC\bin\cl.exe'  
notice: will use 'C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\bin\cl.exe' for msvc, condition <toolset>msvc-14.0  
notice: [msvc-cfg] condition: '<toolset>msvc-14.0/<architecture>/<address-model>', setup: 'call "C:\Users\andrejnau\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_x86.cmd" >nul  
'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.0/<architecture>/<address-model>32', setup: 'call "C:\Users\andrejnau\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_x86.cmd" >nul  
'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.0/<architecture>x86/<address-model>', setup: 'call "C:\Users\andrejnau\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_x86.cmd" >nul  
'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.0/<architecture>x86/<address-model>32', setup: 'call "C:\Users\andrejnau\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_x86.cmd" >nul  
'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.0/<architecture>/<address-model>64', setup: 'call "C:\Users\andrejnau\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_amd64.cmd" >nul  
'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.0/<architecture>x86/<address-model>64', setup: 'call "C:\Users\andrejnau\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_amd64.cmd" >nul  
'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.0/<architecture>ia64/<address-model>', setup: 'call "C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat" x86_ia64 >nul  
'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.0/<architecture>ia64/<address-model>64', setup: 'call "C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat" x86_ia64 >nul  
'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.0/<architecture>arm/<address-model>', setup: 'call "C:\Users\andrejnau\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_x86_arm.cmd" >nul  
'  
notice: [msvc-cfg] condition: '<toolset>msvc-14.0/<architecture>arm/<address-model>32', setup: 'call "C:\Users\andrejnau\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_x86_arm.cmd" >nul  
'  
notice: [zlib] Using pre-installed library  
notice: [zlib] Condition  
notice: [bzip2] Using pre-installed library  
notice: [bzip2] Condition  
notice: [python-cfg] Configuring python...  
notice: [python-cfg] Checking interpreter command "python"...  
notice: [python-cfg] running command 'DIR /-C /A:S "C:\Python27\python.exe" 2>&1'  
notice: [python-cfg] running command 'python -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...requested configuration matched!  
notice: [python-cfg] Details of this Python configuration:  
notice: [python-cfg]   interpreter command: "python"  
notice: [python-cfg]   include path: "C:\Python27\Include"  
notice: [python-cfg]   library path: "C:\Python27\libs"  
notice: [python-cfg]   DLL search path: "C:\Python27"  
notice: [python-cfg] Checking for NumPy...  
notice: [python-cfg] running command 'python -c "import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())"'  
notice: [python-cfg] NumPy enabled  
Performing configuration checks  
  
    - 32-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
  
Building the Boost C++ Libraries.  
  
  
    - symlinks supported       : yes (cached)  
  
Component configuration:  
  
    - atomic                   : not building  
    - chrono                   : not building  
    - container                : not building  
    - context                  : not building  
    - coroutine                : not building  
    - coroutine2               : not building  
    - date_time                : not building  
    - exception                : not building  
    - fiber                    : not building  
    - filesystem               : not building  
    - graph                    : not building  
    - graph_parallel           : not building  
    - iostreams                : not building  
    - locale                   : not building  
    - log                      : not building  
    - math                     : not building  
    - metaparse                : not building  
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
    - type_erasure             : not building  
    - wave                     : not building  
  
...found 1 target...  
  
  
The Boost C++ Libraries were successfully built!  
  
The following directory should be added to compiler include paths:  
  
    C:\boost_1_64_0  
  
The following directory should be added to linker library paths:  
  
    C:\boost_1_64_0\stage\lib  
```

---

## Comment 3

> Username: KayEss  
> Created at: 2017-04-27 01:40:49 UTC  
> Url: https://github.com/boostorg/build/issues/194#issuecomment-297587631  

Are you using boost build bundled with the release, or some other version? I had the same problem until I switched to the version that was included in the release.

---

## Comment 4

> Username: andrejnau  
> Created at: 2017-04-27 05:04:49 UTC  
> Url: https://github.com/boostorg/build/issues/194#issuecomment-297612733  

I am using boost build bundled with the release.  
I think I found the problem. Build was broke after these https://github.com/boostorg/python/commit/8c170d91939f6b6506d3b71715688afc16e4ccc3 changes.  
I just replaced ‪libs/python/build/Jamfile in release 1.64 on [Jamfile bd7b8ec](https://github.com/boostorg/python/blob/bd7b8ecba5c211c3361c4a9af82a7a527bed384e/build/Jamfile) and build boost_python was success.

---

## Comment 5

> Username: jschueller  
> Created at: 2017-05-01 17:41:45 UTC  
> Updated at: 2017-05-01 19:54:05 UTC  
> Url: https://github.com/boostorg/build/issues/194#issuecomment-298383855  

Maybe this should be moved to boostorg/python though ?

---

## Comment 6

> Username: Gollum999  
> Created at: 2017-05-03 20:48:26 UTC  
> Url: https://github.com/boostorg/build/issues/194#issuecomment-299030791  

I ended up tracing this back to my `user-config.jam`.  I was leaving the `version` field of my `using python` rule blank.  The [documentation](http://www.boost.org/doc/libs/1_64_0/libs/python/doc/html/building/configuring_boost_build.html) seems to indicate that this is fine; I think an empty version is supposed to mean either "default version" or "all versions".  However, when leaving the version blank, I see the issue that @andrejnau describes, where no python libraries are built, and no errors or warnings are displayed.  
  
To clarify, this was my full `user-config.jam` where the issue was present:  
  
```  
using python   
    : # version  
    : C:\\Miniconda2\\python.exe  
    : C:\\Miniconda2\\include  
    : C:\\Miniconda2\\libs  
    ;  
```  
And this is the version that works as expected:  
  
```  
using python   
    : 2.7                        # version  
    : C:\\Miniconda2\\python.exe # interpreter  
    : C:\\Miniconda2\\include    # include dir  
    : C:\\Miniconda2\\libs       # library dir  
    ;  
```  
  
It seems to me that if this is expected behavior, **a)** The documentation should be updated to reflect this, and **b)** the Python Jamfile should not be dependent on a potentially uninitialized variable, and should either provide a default or print a warning that no version has been set.

---

## Comment 7

> Username: jschueller  
> Created at: 2017-05-04 10:59:40 UTC  
> Url: https://github.com/boostorg/build/issues/194#issuecomment-299154224  

Indeed @Gollum999, if I use your config file python libs are compiled, thanks.

---

## Comment 8

> Username: agostbiro  
> Created at: 2017-06-02 13:30:25 UTC  
> Updated at: 2017-06-02 13:34:09 UTC  
> Url: https://github.com/boostorg/build/issues/194#issuecomment-305787330  

I had to do the following to make Boost Python 1.64 work in a Docker container based on the [`python:3.5`](https://hub.docker.com/_/python/) container:  
  
```  
# Install Boost 1.64 with Python lib  
  
WORKDIR "/opt"  
  
RUN wget https://sourceforge.net/projects/boost/files/boost/1.64.0/boost_1_64_0.tar.bz2 && \  
    tar --bzip2 -xf boost_1_64_0.tar.bz2 && \  
    rm boost_1_64_0.tar.bz2  
  
RUN cd boost_1_64_0/tools/build && \  
    # Symlink the Python header files to the standard location.  
    # This is important as the base image comes with custom Python 3.5 build   
    # and thus the location of the header files is different.  
    ln -s /usr/local/include/python3.5m /usr/local/include/python3.5 && \  
    ./bootstrap.sh && \  
    ./b2 install --prefix=/opt/boost_build && \  
    ln -s /opt/boost_build/bin/b2 /usr/bin/b2 && \  
    ln -s /opt/boost_build/bin/bjam /usr/bin/bjam && \  
    cd /opt/boost_1_64_0 && \  
    # Specify Python version explicitly as solution to https://github.com/boostorg/build/issues/194  
    echo "using python : 3.5 ;" >> /etc/site-config.jam && \  
    b2 --with-python toolset=gcc stage  
```
