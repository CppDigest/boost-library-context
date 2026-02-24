# #631 - bootstrap will select wrong vcvars when multiple Visual Studio's are available and build will fail [Closed]

> Username: dickvdm  
> Created at: 2020-06-26 14:49:53 UTC  
> Updated at: 2020-06-26 15:06:27 UTC  
> Closed at: 2020-06-26 15:06:26 UTC  
> Url: https://github.com/boostorg/build/issues/631  

Suppose:  
C:\Program Files (x86)\Microsoft Visual Studio\Installer>vswhere -property displayname  
Visual Studio Enterprise 2017  
Visual Studio Professional 2019  
Visual Studio Enterprise 2019  
  
From Developer Command Prompt for Visual Studio 2019, run _bootstrap_  
Even when vcvars from Visual Studio 2019 are loaded, bootstrap.log will show:  
_Call_If_Exists "C:\Program Files (x86)\Microsoft Visual Studio\2017\Enterprise\Common7\Tools\..\..\VC\Auxiliary\Build\vcvarsall.bat"  x86  
**********************************************************************  
** Visual Studio 2017 Developer Command Prompt v15.9.11  
** Copyright (c) 2017 Microsoft Corporation  
**********************************************************************  
[vcvarsall.bat] Environment initialized for: 'x86'  
###  
### Using 'vc141' toolset.  
###_  
  
This is due to vswhere_usability_wrapper.cmd, that finds vs2017 first, and then stops searching for vs2019.  
  
Now run _b2 toolset=msvc-14.2_  
  
This will result in:  
_LINK : fatal error LNK1101: incorrect MSPDB140.DLL version; recheck installation of this product_  
  
Expect to be using VS2019 environment here and successful build and link.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-06-26 15:06:26 UTC  
> Url: https://github.com/boostorg/build/issues/631#issuecomment-650229638  

Fixed in https://github.com/boostorg/build/commit/a3bdb4fd98996d2564eed838b72046a1e99bf367
