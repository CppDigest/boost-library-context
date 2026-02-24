# #132 - No library built on Windows. [Closed]

> Username: KelSolaar  
> Created at: 2017-06-07 21:48:32 UTC  
> Updated at: 2017-07-20 12:09:22 UTC  
> Closed at: 2017-07-20 12:09:22 UTC  
> Url: https://github.com/boostorg/python/issues/132  

Hi,  
  
I'm trying to build Boost.Python from Boost 1.64.0 on Windows 10 using MSVC 2015 and while the compilation seems to be successful I can't find any built library anywhere.  
  
My invocation command and log is as follows:  
  
```  
[localhost] local: call vcbuildtools.bat amd64 && cd /d D:\Documents\Development\ThirdParty\boost/build\boost && call bootstrap.bat --with-python=K:\Packages\python\2.7.13\platform-windows\arch-AMD64 && b2 toolset=msvc-14.0 address-model=64 --with-python -j8  
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
  
    D:\Documents\Development\ThirdParty\boost\build\boost  
  
The following directory should be added to linker library paths:  
  
    D:\Documents\Development\ThirdParty\boost\build\boost\stage\lib  
  
  
Done.  
  
All 1 build(s) were successful.  
```  
  
and the *bootstrap.log* file content is as follows:  
  
```  
###  
### Using 'vc14' toolset.  
###  
  
> CLINK D:\Documents\Development\ThirdParty\boost\build\boost\tools\build\src\engine>if exist bootstrap rd /S /Q bootstrap   
  
> CLINK D:\Documents\Development\ThirdParty\boost\build\boost\tools\build\src\engine>md bootstrap   
  
> CLINK D:\Documents\Development\ThirdParty\boost\build\boost\tools\build\src\engine>cl /nologo /RTC1 /Zi /MTd /Fobootstrap/ /Fdbootstrap/ -DNT -DYYDEBUG -wd4996 kernel32.lib advapi32.lib user32.lib /Febootstrap\jam0  command.c compile.c constants.c debug.c execcmd.c execnt.c filent.c frames.c function.c glob.c hash.c hdrmacro.c headers.c jam.c jambase.c jamgram.c lists.c make.c make1.c object.c option.c output.c parse.c pathnt.c pathsys.c regexp.c rules.c scan.c search.c subst.c timestamp.c variable.c modules.c strings.c filesys.c builtins.c md5.c class.c cwd.c w32_getreg.c native.c modules/set.c modules/path.c modules/regex.c modules/property-set.c modules/sequence.c modules/order.c   
command.c  
compile.c  
constants.c  
debug.c  
execcmd.c  
execnt.c  
filent.c  
filent.c(488): warning C4477: 'sprintf' : format string '%.*s' requires an argument of type 'int', but variadic argument 1 has type '__int64'  
filent.c(488): note: this argument is used as a precision  
frames.c  
function.c  
glob.c  
hash.c  
hdrmacro.c  
headers.c  
jam.c  
jambase.c  
jamgram.c  
lists.c  
make.c  
make1.c  
object.c  
Generating Code...  
Compiling...  
option.c  
output.c  
parse.c  
pathnt.c  
pathsys.c  
regexp.c  
rules.c  
scan.c  
search.c  
subst.c  
timestamp.c  
variable.c  
modules.c  
strings.c  
filesys.c  
builtins.c  
md5.c  
class.c  
cwd.c  
w32_getreg.c  
Generating Code...  
Compiling...  
native.c  
set.c  
path.c  
regex.c  
property-set.c  
sequence.c  
order.c  
Generating Code...  
  
> CLINK D:\Documents\Development\ThirdParty\boost\build\boost\tools\build\src\engine>.\bootstrap\jam0 -f build.jam --toolset=vc14 "--toolset-root=C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\ " --with-python=K:\Packages\python\2.7.13\platform-windows\arch-AMD64 clean   
Python includes: K:\Packages\python\2.7.13\platform-windows\arch-AMD64\include  
Python includes: K:\Packages\python\2.7.13\platform-windows\arch-AMD64\libs\python27.lib  
...found 1 target...  
...updating 1 target...  
...updated 1 target...  
  
> CLINK D:\Documents\Development\ThirdParty\boost\build\boost\tools\build\src\engine>.\bootstrap\jam0 -f build.jam --toolset=vc14 "--toolset-root=C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\ " --with-python=K:\Packages\python\2.7.13\platform-windows\arch-AMD64   
Python includes: K:\Packages\python\2.7.13\platform-windows\arch-AMD64\include  
Python includes: K:\Packages\python\2.7.13\platform-windows\arch-AMD64\libs\python27.lib  
...found 159 targets...  
...updating 3 targets...  
[MKDIR] bin.ntx86_64  
[COMPILE] bin.ntx86_64\b2.exe  
command.c  
compile.c  
constants.c  
debug.c  
execcmd.c  
frames.c  
function.c  
glob.c  
hash.c  
hcache.c  
headers.c  
hdrmacro.c  
jam.c  
jambase.c  
jamgram.c  
lists.c  
make.c  
make1.c  
mem.c  
object.c  
Compiling...  
option.c  
output.c  
parse.c  
pathsys.c  
regexp.c  
rules.c  
scan.c  
search.c  
subst.c  
w32_getreg.c  
timestamp.c  
variable.c  
modules.c  
strings.c  
filesys.c  
builtins.c  
class.c  
cwd.c  
native.c  
md5.c  
Compiling...  
set.c  
path.c  
regex.c  
property-set.c  
sequence.c  
order.c  
execnt.c  
filent.c  
filent.c(488): warning C4477: 'sprintf' : format string '%.*s' requires an argument of type 'int', but variadic argument 1 has type '__int64'  
filent.c(488): note: this argument is used as a precision  
pathnt.c  
Generating code  
Finished generating code  
[COPY] bin.ntx86_64\bjam.exe  
...updated 3 targets...  
  
> CLINK D:\Documents\Development\ThirdParty\boost\build\boost\tools\build\src\engine>exit /b 0   
```

---

## Comment 1

> Username: KelSolaar  
> Created at: 2017-06-08 04:26:11 UTC  
> Url: https://github.com/boostorg/python/issues/132#issuecomment-306994771  

I have run with `--debug-configuration` and the part for Python seems to be working:  
  
```  
notice: [python-cfg] Configuring python...  
notice: [python-cfg] Checking interpreter command "python"...  
notice: [python-cfg] running command 'DIR /-C /A:S "K:\Packages\Fishbowl\python_fishbowl\2.7.13\platform-windows\arch-AMD64\python.exe" 2>&1'  
notice: [python-cfg] running command 'python -c "from sys import *; print('version=%d.%d\nplatform=%s\nprefix=%s\nexec_prefix=%s\nexecutable=%s' % (version_info[0],version_info[1],platform,prefix,exec_prefix,executable))" 2>&1'  
notice: [python-cfg] ...requested configuration matched!  
notice: [python-cfg] Details of this Python configuration:  
notice: [python-cfg]   interpreter command: "python"  
notice: [python-cfg]   include path: "K:\Packages\Fishbowl\python_fishbowl\2.7.13\platform-windows\arch-AMD64\Include"  
notice: [python-cfg]   library path: "K:\Packages\Fishbowl\python_fishbowl\2.7.13\platform-windows\arch-AMD64\libs"  
notice: [python-cfg]   DLL search path: "K:\Packages\Fishbowl\python_fishbowl\2.7.13\platform-windows\arch-AMD64"  
notice: [python-cfg] Checking for NumPy...  
notice: [python-cfg] running command 'python -c "import sys; sys.stderr = sys.stdout; import numpy; print(numpy.get_include())"'  
notice: [python-cfg] NumPy enabled  
```

---

## Comment 2

> Username: KelSolaar  
> Created at: 2017-06-08 05:49:31 UTC  
> Url: https://github.com/boostorg/python/issues/132#issuecomment-307005369  

Confirming that the build worked with Boost 1.63.0, I also grabbed bd7b8ec to fix Numpy related errors.

---

## Comment 3

> Username: sav-ix  
> Created at: 2017-06-25 18:36:00 UTC  
> Updated at: 2017-07-17 12:05:49 UTC  
> Url: https://github.com/boostorg/python/issues/132#issuecomment-310919753  

Hello everyone,  
  
Got similar issue with recent Boost release.  
Reproduced for builds using mingw-w64 6.3.0, ICC 2017 and MSVC 2015. A fixed [Jamfile](https://github.com/boostorg/python/blob/bd7b8ecba5c211c3361c4a9af82a7a527bed384e/build/Jamfile) allow to bypass it, but for some reason it didn't appear neither in Boots-1.64.0 nor in Boots-1.65.0-beta1_rc2.  
Could you push it to Boots.Python master?  
  
  
Alexander

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2017-07-19 22:05:18 UTC  
> Url: https://github.com/boostorg/python/issues/132#issuecomment-316532260  

A fix recently made it into Boost.Build that may be related to the above:  https://github.com/boostorg/build/pull/212  
This is merged into Boost.Build's develop branch. Could you please try to confirm that the issue is resolved with that ? If so, I'll remind them to merge this fix into master.  
  
Thanks,

---

## Comment 5

> Username: sav-ix  
> Created at: 2017-07-20 06:18:14 UTC  
> Updated at: 2017-07-20 06:20:26 UTC  
> Url: https://github.com/boostorg/python/issues/132#issuecomment-316607897  

Just tested Boost.Python builds using mingw-w64 and MSVC with `<Debug|Release><Shared|Static>` configurations and https://github.com/boostorg/build/pull/212/commits/4f521b5b97371869fb18e35c7f0af94644d57d48 applied, and in all cases it solved this issue.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2017-07-20 12:09:22 UTC  
> Url: https://github.com/boostorg/python/issues/132#issuecomment-316683830  

Thanks for confirming this. I'll look into what it takes to merge this into the master branch for the release.
