# #140 - ''::hypot' has not been declared' and 'undefined reference to `__imp_Py_InitModule4'' errors for mingw-w64 builds on Windows [Closed]

> Username: sav-ix  
> Created at: 2017-07-17 17:32:24 UTC  
> Updated at: 2019-03-24 00:37:36 UTC  
> Closed at: 2019-03-24 00:37:36 UTC  
> Url: https://github.com/boostorg/python/issues/140  

Hello everyone,  
  
For Boost.Python builds using mingw-w64 on Windows got error:  
```  
.\b2 --build-type=complete --with-python toolset=gcc-6.3.0 variant=release link=shared threading=multi runtime-link=shared address-model=64 -j1 -d2 -q  
  
<snip>  
  
gcc.compile.c++ bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\numeric.o  
  
    "g++"  -ftemplate-depth-128 -O3 -finline-functions -Wno-inline -Wall -mthreads -m64  -DBOOST_ALL_NO_LIB=1 -DBOOST_PYTHON_SOURCE -DNDEBUG  -I"." -I"C:\ProgramData\Python64\Include" -c -o "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\numeric.o" "libs\python\src\numeric.cpp"  
  
In file included from C:/ProgramData/Mingw64/lib/gcc/x86_64-w64-mingw32/6.3.0/include/c++/math.h:36:0,  
                 from C:\ProgramData\Python64\Include/pyport.h:325,  
                 from C:\ProgramData\Python64\Include/Python.h:58,  
                 from ./boost/python/detail/wrap_python.hpp:151,  
                 from ./boost/python/detail/prefix.hpp:13,  
                 from ./boost/python/numeric.hpp:8,  
                 from libs\python\src\numeric.cpp:6:  
C:/ProgramData/Mingw64/lib/gcc/x86_64-w64-mingw32/6.3.0/include/c++/cmath:1157:11: error: '::hypot' has not been declared  
   using ::hypot;  
           ^~~~~  
  
<snip>  
  
...failed gcc.compile.c++ bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\numeric.o...  
...failed updating 1 target...  
...updated 8 targets...  
```  
  
Reproduced for builds using mingw-w64 with `<Debug|Release> <Shared|Static>` configurations. Not reproduced for builds using ICC or MSVC.  
  
Could be fixed by adding `cxxflags="-D_hypot=hypot"` key to Boost build commands (see [appropriate topic](http://stackoverflow.com/questions/10660524/error-building-boost-1-49-0-with-gcc-4-7-0)).  
  
If blocker above is fixed, next error appear:  
```  
.\b2 cxxflags="-D_hypot=hypot" --build-type=complete --with-python toolset=gcc-6.3.0 variant=release link=shared threading=multi runtime-link=shared address-model=64 -j1 -d2 -q  
  
<snip>  
  
gcc.link.dll bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\libboost_python-mgw63-mt-1_64.dll.a  
  
    "g++" -L"C:\ProgramData\Python64\libs" -Wl,-R -Wl,"C:\ProgramData\Python64" -Wl,-R -Wl,"C:\ProgramData\Python64\libs" "-Wl,--out-implib,bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\libboost_python-mgw63-mt-1_64.dll.a" -o "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\libboost_python-mgw63-mt-1_64.dll"  -shared -Wl,--start-group "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\numeric.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\list.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\long.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\dict.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\tuple.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\str.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\slice.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\converter\from_python.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\converter\registry.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\converter\type_id.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\object\enum.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\object\class.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\object\function.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\object\inheritance.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\object\life_support.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\object\pickle_support.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\errors.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\module.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\converter\builtin_converters.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\converter\arg_to_python_base.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\object\iterator.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\object\stl_iterator.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\object_protocol.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\object_operators.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\wrapper.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\import.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\exec.o" "bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\object\function_doc_signature.o"  -Wl,-Bstatic  -Wl,-Bdynamic -lpython27 -Wl,--end-group -mthreads -m64   
  
bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\module.o:module.cpp:(.text+0xf1): undefined reference to `__imp_Py_InitModule4'  
collect2.exe: error: ld returned 1 exit status  
...failed gcc.link.dll bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\libboost_python-mgw63-mt-1_64.dll.a bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\libboost_python-mgw63-mt-1_64.dll...  
...removing bin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi\libboost_python-mgw63-mt-1_64.dll.a  
...skipped <pstage\lib>libboost_python-mgw63-mt-1_64.dll.a for lack of <pbin.v2\libs\python\build\gcc-mingw-6.3.0\release\threading-multi>libboost_python-mgw63-mt-1_64.dll.a...  
...failed updating 1 target...  
...skipped 1 target...  
...updated 30 targets...  
```  
  
Reproduced for builds using mingw-w64 with `<Debug|Release> Shared` configurations. Not reproduced for builds using ICC or MSVC.  
  
Could be fixed by adding `cxxflags="-DMS_WIN64"` key to Boost build commands (see [appropriate topic](http://stackoverflow.com/questions/2842469/python-undefined-reference-to-imp-py-initmodule4)).  
  
Both errors above relate to the Python implementation because of definitions:  
```  
#define hypot _hypot  
```  
and  
```  
#if defined(MS_WIN64) ... #endif  
```  
in file `<Python dir>/include/pyconfig.h`  
  
Is it possible to fix this issue? E.g. add mentioned preprocessor definitions to Boost.Python [build system](https://github.com/boostorg/python/blob/develop/build/Jamfile) as a separate rule for mingw-w64 builds on Windows, or something.  
  
Environment:  
- Windows 10 x64,  
- mingw-w64 6.3.0 x86_64,  
- Python 2.7.13,  
- Boost-1.64.0.  
  
  
Alexander

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-07-20 14:57:00 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-316729648  

I'm inclined to remove the numeric module entirely, as we now have Boost.NumPy. Are you a user of the numeric module, or only reporting the build failure ?

---

## Comment 2

> Username: sav-ix  
> Created at: 2017-07-20 14:58:46 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-316730218  

Just reporting build failure, since it broke whole Boost.Python module build.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-07-20 17:30:55 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-316775204  

OK, I have removed the `numeric` module from the `develop` branch (and will merge this soon to `master`, too. Does this fix your build ?

---

## Comment 4

> Username: sav-ix  
> Created at: 2017-07-20 18:08:26 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-316785313  

Downloaded fresh Boost.Python dev branch, made sure it contain PR https://github.com/boostorg/python/commit/2d1f66fd1948fa28fde05f4c533b13fb3c96d782, made builds using mingw-w64 with `Release+<Shared|Static>` configurations. Both cases finished with error `'::hypot' has not been declared`.

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2017-07-20 18:15:11 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-316787041  

Have you considered reporting this issue upstream, to `Python` itself ? Is Python never built with `mingw-w64` ?

---

## Comment 6

> Username: sav-ix  
> Created at: 2017-07-20 18:33:09 UTC  
> Updated at: 2017-07-20 18:36:26 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-316791655  

Nope, didn't consider. But since `#define hypot _hypot` definition is a feature of Python (`pyconfig.h`) implementation, it's hard to expect positive result.  
I assumed, that solution is to add required build keys to Boost.Python toolchains, similar to other OSS libraries do. E.g. MESA added `0x0601` key into mingw-w64 [toolchain](https://cgit.freedesktop.org/mesa/mesa/tree/scons/gallium.py) in order to "unlock" required mingw-w64 functionality, not available by default, etc.  
I don't claim to offer the best solution. But at least it allows to build Boots.Python using mingw-w64 without errors.

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2017-07-20 18:38:24 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-316793057  

Here is an existing issue: https://bugs.python.org/issue11566, just for the record.

---

## Comment 8

> Username: stefanseefeld  
> Created at: 2017-07-20 18:42:29 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-316794104  

...plus a PR: https://github.com/python/cpython/pull/880. It looks like this is a bit more involved as CPython and NumPy have different ideas as to how hypot() should behave...

---

## Comment 9

> Username: sav-ix  
> Created at: 2017-07-20 18:46:48 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-316795204  

[_Created on 2011-03-15_](https://bugs.python.org/issue11566). A long play issue.  
Since it was taken as Python bug and some PR already introduced, it make sense to pause this issue for some time. In case that PR would pass review and would be used in future Python releases.  
But even then, how would be fixed `undefined reference to __imp_Py_InitModule4` error?

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2017-07-20 18:53:36 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-316796989  

I think that issue is indeed solved by adding `-DMS_WIN64` to the command line. It seems that ought to be done by Boost.Build for all instances of mingw64, not in Boost.Python. I hope @raffienficiaud or @grafikrobot can help.

---

## Comment 11

> Username: sav-ix  
> Created at: 2017-07-20 19:03:01 UTC  
> Updated at: 2017-07-20 19:03:44 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-316799490  

`MS_WIN64 ` definition is a Python-related only. Mean Python introduced it for its own purposes, and [compilers](https://sourceforge.net/p/predef/wiki/Home/) know nothing about it. E.g. no sense to add it anywhere except Boost.Python builds.

---

## Comment 12

> Username: stefanseefeld  
> Created at: 2017-07-20 19:11:37 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-316801674  

Then perhaps it should be defined in `boost/python/detail/config.hpp` ?

---

## Comment 13

> Username: eldiener  
> Created at: 2018-10-18 14:37:23 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-431034101  

I also see this issue trying to build Boost.Python with mingw-644/gcc-8.1 on Windows. Adding a _DMS_WIN64 to the command line does not solve anything. I will assume that building Boost.Python with mingw-64/gcc is just broke.

---

## Comment 14

> Username: eldiener  
> Created at: 2018-10-18 14:40:14 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-431035195  

I should add in my report that I am using Python 2.7.13.

---

## Comment 15

> Username: nihir27  
> Created at: 2018-10-19 22:43:44 UTC  
> Url: https://github.com/boostorg/python/issues/140#issuecomment-431519697  

I'm experiencing the same with Python 3.6.5
