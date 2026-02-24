# #725 - Boost.NumPy doesn't build when more than one Python version is configured in user-config [Closed]

> Username: pdimov  
> Created at: 2021-05-24 18:50:09 UTC  
> Updated at: 2021-06-04 12:42:42 UTC  
> Closed at: 2021-06-04 12:42:41 UTC  
> Url: https://github.com/boostorg/build/issues/725  

When user-config.jam has more than one version of Python configured, e.g.  
```  
using python : 2.7 : C:/Python27 ;  
using python : 3.9 : C:/Python39 ;  
```  
trying to build Python (with e.g. `b2 --with-python python=2.7`) fails, because Boost.NumPy is built against the (correct) 2.7 version of Python, but against the 3.9 headers of NumPy:  
```  
cl /Zm800 -nologo "libs\python\src\numpy\ufunc.cpp" -Fo"bin.v2\libs\python\build\msvc-14.2\debug\address-model-32\link-static\python-2.7\threading-multi\numpy\ufunc.obj"    -TP /wd4675 /EHs /GR /Zc:throwingNew /Z7 /Od /Ob0 /W3 /MDd /Zc:forScope /Zc:wchar_t /Zc:inline -c -DBOOST_ALL_NO_LIB=1 -DBOOST_NUMPY_SOURCE -DBOOST_NUMPY_STATIC_LIB -DBOOST_PYTHON_STATIC_LIB "-I." "-IC:\Python27\Include" "-IC:\Python39\lib\site-packages\numpy\core\include"  
```  
The reason for that is that the Python configuration in b2's `python.jam` stores the NumPy include path in a variable `.numpy-include`, which can only hold one value.  
  
https://github.com/boostorg/build/blob/916afd3876bc0d5b706a63f9d09ba66ff69e7d66/src/tools/python.jam#L1087-L1090  
https://github.com/boostorg/build/blob/916afd3876bc0d5b706a63f9d09ba66ff69e7d66/src/tools/python.jam#L858  
  
This can in principle be fixed by declaring a target for NumPy's include path, instead of a variable, and then linking to it instead of using `<include>$(numpy-include)` in build/Jamfile.  
  
https://github.com/boostorg/python/blob/2a82afdf6d30c28eb1775f3b8041d988f6f04598/build/Jamfile#L120

---

## Comment 1

> Username: pdimov  
> Created at: 2021-05-24 18:50:32 UTC  
> Url: https://github.com/boostorg/build/issues/725#issuecomment-847258241  

Boost.Python issue: https://github.com/boostorg/python/issues/361

---

## Comment 2

> Username: SoapGentoo  
> Created at: 2021-05-24 21:20:05 UTC  
> Url: https://github.com/boostorg/build/issues/725#issuecomment-847342847  

The original Gentoo issue with a comment where the bug occurs: https://bugs.gentoo.org/733830#c12

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:13 UTC  
> Url: https://github.com/boostorg/build/issues/725#issuecomment-850859551  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
