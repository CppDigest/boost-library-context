# #257 - Upgrading msvc-14.1 to 15.4.2 results in stddef.h not found [Closed]

> Username: pdimov  
> Created at: 2017-11-01 18:21:57 UTC  
> Updated at: 2018-01-23 03:49:36 UTC  
> Closed at: 2018-01-23 03:49:36 UTC  
> Url: https://github.com/boostorg/build/issues/257  

Upgrading Visual Studio 2017 to 15.4.2 causes  
  
```  
C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.11.25503\include\cstddef(5): fatal error C1083: Cannot open include file: 'stddef.h': No such file or directory  
```  
  
This is fixed by deleting the setup scripts `b2_msvc_14.1_vcvars32_.cmd` and `b2_msvc_14.1_vcvarsx86_amd64_.cmd` in %TEMP% to force their being regenerated.  
  
Shouldn't these scripts be kept in `bin.v2` instead? It's customary to delete `bin.v2` as a troubleshooting step, but hunting for things in %TEMP% is less intuitive.

---

## Comment 1

> Username: swatanabe  
> Created at: 2017-11-01 19:28:49 UTC  
> Url: https://github.com/boostorg/build/issues/257#issuecomment-341213579  

AMDG  
  
On 11/01/2017 12:21 PM, Peter Dimov wrote:  
>   
> Shouldn't these scripts be kept in `bin.v2` instead? It's customary to delete `bin.v2` as a troubleshooting step, but hunting for things in %TEMP% is less intuitive.  
>   
  
+1.  I have no idea why it wasn't done that way in the first place.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: swatanabe  
> Created at: 2017-12-14 19:53:48 UTC  
> Url: https://github.com/boostorg/build/issues/257#issuecomment-351818172  

I've started working on this, but the msvc toolset is in need of quite a lot of cleanup.
