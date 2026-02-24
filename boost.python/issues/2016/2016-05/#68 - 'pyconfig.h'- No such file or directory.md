# #68 - 'pyconfig.h': No such file or directory [Closed]

> Username: OlafvdSpek  
> Created at: 2016-05-25 08:27:41 UTC  
> Updated at: 2017-07-27 16:44:40 UTC  
> Closed at: 2017-07-27 16:44:39 UTC  
> Url: https://github.com/boostorg/python/issues/68  

Boost 1.61 @ VS2015:  
  
`    call "C:\Users\Olaf\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_x86.cmd" >nul  
cl /Zm800 -nologo @"bin.v2\libs\python\build\msvc-14.0\debug\link-static\threading-multi\exec.obj.rsp"  
  
...failed compile-c-c++ bin.v2\libs\python\build\msvc-14.0\debug\link-static\threading-multi\exec.obj...  
compile-c-c++ bin.v2\libs\python\build\msvc-14.0\debug\link-static\threading-multi\object\function_doc_signature.obj  
function_doc_signature.cpp  
C:\VC\boost\boost/python/detail/wrap_python.hpp(50): fatal error C1083: Cannot open include file: 'pyconfig.h': No such file or directory  
  
```  
call "C:\Users\Olaf\AppData\Local\Temp\b2_msvc_14.0_vcvarsall_x86.cmd" >nul  
```  
  
cl /Zm800 -nologo @"bin.v2\libs\python\build\msvc-14.0\debug\link-static\threading-multi\object\function_doc_signature.obj.rsp"  
  
...failed compile-c-c++ bin.v2\libs\python\build\msvc-14.0\debug\link-static\threading-multi\object\function_doc_signature.obj...  
...skipped <pbin.v2\libs\python\build\msvc-14.0\debug\link-static\threading-multi>libboost_python-vc140-mt-gd-1_61.lib for lack of <pbin.v2\libs\python\build\msvc-14.0\debug\link-static\threading-multi>numeric.obj...  
...skipped <pstage\lib>libboost_python-vc140-mt-gd-1_61.lib for lack of <pbin.v2\libs\python\build\msvc-14.0\debug\link-static\threading-multi>libboost_python-vc140-mt-gd-1_61.lib...  
...failed updating 56 targets...  
...skipped 4 targets...`

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2016-11-28 18:37:19 UTC  
> Url: https://github.com/boostorg/python/issues/68#issuecomment-263354576  

It's hard to tell what exactly goes wrong, but it seems clear that the problem is that Python itself (specifically, its main header file) isn't found. Make sure you configure your build correctly by indicating the path to your Python installation.

---

## Comment 2

> Username: macheeto  
> Created at: 2017-07-27 16:40:15 UTC  
> Url: https://github.com/boostorg/python/issues/68#issuecomment-318417979  

This problem is also tracked here:  
https://svn.boost.org/trac10/ticket/11120#comment:21  
There are useful hints in that ticket.

---

## Comment 3

> Username: OlafvdSpek  
> Created at: 2017-07-27 16:44:39 UTC  
> Url: https://github.com/boostorg/python/issues/68#issuecomment-318419161  

> Make sure you configure your build correctly by indicating the path to your Python installation.  
  
Python isn't installed..   
I guess it's a problem in the Python detection code that has since been solved.
