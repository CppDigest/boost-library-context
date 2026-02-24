# #420 - Autodiscovered Python will be used for building even if it is of different bitness [Closed]

> Username: Kojoley  
> Created at: 2019-03-31 16:09:45 UTC  
> Updated at: 2025-04-18 23:28:55 UTC  
> Closed at: 2025-04-18 23:28:55 UTC  
> Url: https://github.com/boostorg/build/issues/420  

32bit Python is installed in `C:\Python27`, but `b2 address-model=64 toolset=msvc-14.1 python` tries build it, and of course fails:  
```  
C:\Python27\libs\python27.lib : warning LNK4272: library machine type 'x86' conflicts with target machine type 'x64'  
bin.v2\libs\python\build\msvc-14.1\debug\address-model-64\python-2.7\threading-multi\boost_python27-vc141-mt-gd-x64-1_70.dll : fatal error LNK1120: 135 unresolved externals  
```  
  
The reverse problem (64bit Python and 32bit MSVC) seems to happen on regression matrix https://www.boost.org/development/tests/develop/developer/output/BI-msvc-14-0-boost-bin-v2-libs-python-test-andreas_beyer-test-msvc-14-0-dbg-pythn-2-7-thrd-mlt.html

---

## Comment 1

> Username: Kojoley  
> Created at: 2019-03-31 16:36:27 UTC  
> Url: https://github.com/boostorg/build/issues/420#issuecomment-478357102  

Probably Python launcher `py` can be used to find a correct one.  
  
```  
>py -0  
Installed Pythons found by py Launcher for Windows  
 -3.7-64 *  
 -3.6-64  
 -3.5-64  
 -2.7-64  
 -2.7-32  
```

---

## Comment 2

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:10 UTC  
> Url: https://github.com/boostorg/build/issues/420#issuecomment-859203405  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
