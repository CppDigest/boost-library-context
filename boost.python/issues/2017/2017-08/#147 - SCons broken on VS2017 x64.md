# #147 - SCons broken on VS2017 x64 [Closed]

> Username: garyfurnish  
> Created at: 2017-08-21 08:06:07 UTC  
> Updated at: 2017-08-21 15:31:12 UTC  
> Closed at: 2017-08-21 15:31:12 UTC  
> Url: https://github.com/boostorg/python/issues/147  

In the vs 2017 x64 command prompt used to build boost:  
```  
D:\dev\boost\libs\python>c:\Python27\Scripts\scons  --boost-prefix=../../  
scons: Reading SConscript files ...  
  
scons: warning: No version of Visual Studio compiler found - C/C++ compilers most likely not set correctly  
File "D:\dev\boost\libs\python\SConstruct", line 47, in <module>  
  
scons: warning: No version of Visual Studio compiler found - C/C++ compilers most likely not set correctly  
File "D:\dev\boost\libs\python\SConstruct", line 60, in <module>  
KeyError: 'MSVS_VERSION':  
  File "D:\dev\boost\libs\python\SConstruct", line 65:  
    build_dir = config.prepare_build_dir(env)  
  File "D:\dev\boost\libs\python\config\__init__.py", line 110:  
    build_dir+="/msvc-%s"%env["MSVS_VERSION"]  
  File "c:\python27\lib\site-packages\scons-2.5.1\SCons\Environment.py", line 410:  
    return self._dict[key]  
```  
  
This prevents accurately testing python on x64 systems.

---

## Comment 1

> Username: garyfurnish  
> Created at: 2017-08-21 14:52:36 UTC  
> Url: https://github.com/boostorg/python/issues/147#issuecomment-323764275  

This appears to at least partially be a bug in SCons that might be fixed by a new beta version...

---

## Comment 2

> Username: stefanseefeld  
> Created at: 2017-08-21 15:07:29 UTC  
> Url: https://github.com/boostorg/python/issues/147#issuecomment-323768812  

Just FYI, I'm working on an new build tool (a clone of Boost.Build: https://github.com/stefanseefeld/faber), so I'm not sure how much energy I'm going to put into the SCons-based build system for Boost.Python.

---

## Comment 3

> Username: garyfurnish  
> Created at: 2017-08-21 15:31:12 UTC  
> Url: https://github.com/boostorg/python/issues/147#issuecomment-323775985  

This is fixed in the latest scons mercurial trunk.
