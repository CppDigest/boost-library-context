# #129 - Windows prebuilt binaries v1.64.0: boost-python3 is linked against python27.dll [Closed]

> Username: krf  
> Created at: 2017-05-26 10:53:25 UTC  
> Updated at: 2018-04-09 01:28:07 UTC  
> Closed at: 2018-04-09 01:28:07 UTC  
> Url: https://github.com/boostorg/python/issues/129  

Installer from: https://sourceforge.net/projects/boost/files/boost-binaries/1.64.0/boost_1_64_0-msvc-14.0-64.exe/download  
  
When linking against boost-python3, compilation fails since boost-python3 pulls in Python 2.7 instead of Python 3.x:  
  
```  
foo.cpp.obj : error LNK2019: unresolved external symbol "__declspec(dllimport) struct _object * __cdecl boost::python::detail::init_module(struct PyModuleDef &,void (__cdecl*)(void))" (__imp_?init_module@detail@python@boost@@YAPEAU_object@@AEAUPyModuleDef@@P6AXXZ@Z) referenced in function PyInit_window  
  
..\bin\foolib.dll : fatal error LNK1120: 1 unresolved externals  
```  
  
```  
C:\>dumpbin /dependents C:\local\boost_1_64_0\lib64-msvc-14.0\boost_python3-vc140-mt-1_64.dll  
Microsoft (R) COFF/PE Dumper Version 10.00.40219.01  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
  
Dump of file C:\local\boost_1_64_0\lib64-msvc-14.0\boost_python3-vc140-mt-1_64.dll  
  
File Type: DLL  
  
  Image has the following dependencies:  
  
    python27.dll  
    MSVCP140.dll  
    KERNEL32.dll  
    VCRUNTIME140.dll  
    api-ms-win-crt-runtime-l1-1-0.dll  
    api-ms-win-crt-heap-l1-1-0.dll  
  
  Summary  
  
        3000 .data  
        1000 .gfids  
        3000 .pdata  
       1E000 .rdata  
        1000 .reloc  
        1000 .rsrc  
       24000 .text  
        1000 .tls  
```

---

## Comment 1

> Username: stefanseefeld  
> Created at: 2017-06-27 21:37:46 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-311493660  

Would you please try the current `develop` or `master` (at this point they are the same) and report whether this is still an issue ? Thanks !

---

## Comment 2

> Username: nickdademo  
> Created at: 2017-08-25 14:55:51 UTC  
> Updated at: 2017-08-25 14:58:31 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-324944867  

I'm getting the same problem with **boost_1_65_0-msvc-14.0-64**, so I guess this problem still exists...  
  
```  
dumpbin /dependents boost_python-vc140-mt-1_65.dll  
  
Microsoft (R) COFF/PE Dumper Version 14.00.24215.1  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
  
Dump of file boost_python-vc140-mt-1_65.dll  
  
File Type: DLL  
  
  Image has the following dependencies:  
  
    python27.dll  
    MSVCP140.dll  
    KERNEL32.dll  
    VCRUNTIME140.dll  
    api-ms-win-crt-runtime-l1-1-0.dll  
    api-ms-win-crt-heap-l1-1-0.dll  
  
  Summary  
  
        3000 .data  
        1000 .gfids  
        3000 .pdata  
       1B000 .rdata  
        1000 .reloc  
        1000 .rsrc  
       22000 .text  
        1000 .tls  
```  
Edit: **boost_python3-vc140-mt-1_65.dll** also links to Python 2.7.

---

## Comment 3

> Username: stefanseefeld  
> Created at: 2017-08-25 15:43:39 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-324958591  

Thanks for the report; I can confirm that. However, I can't reproduce the problem by building Boost.Python myself. I'll try to figure out who "owns" the process of building the Windows binaries.  
Meanwhile, I suggest building Boost (or at least Boost.Python) from source.

---

## Comment 4

> Username: garyfurnish  
> Created at: 2017-08-25 16:06:42 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-324965129  

I also didn't reproduce this a few days ago.

---

## Comment 5

> Username: nickdademo  
> Created at: 2017-08-25 16:11:28 UTC  
> Updated at: 2017-08-25 16:11:49 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-324966340  

Looks like my only option currently is to build from source as you suggest Stefan.  
  
I assume the dependency to Python 2.7 in **boost_python3-vc140-mt-1_65.dll** is indeed a mistake? I wonder how Boost.Python is working for others with the same setup.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2017-08-25 16:16:47 UTC  
> Updated at: 2017-08-25 16:17:09 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-324967699  

> I assume the dependency to Python 2.7 in  
> *boost_python3-vc140-mt-1_65.dll* is indeed a mistake?  
>  
  
Yes of course ! boost_python3 is supposed to be a library built against  
Python3, not Python2.

---

## Comment 7

> Username: nickdademo  
> Created at: 2017-08-27 03:18:12 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-325174553  

Just a quick follow-up - as expected, building the Boost.Python library from source fixed the issue:  
```  
  
dumpbin /dependents boost_python3-vc140-mt-1_65.dll  
  
Microsoft (R) COFF/PE Dumper Version 14.00.24215.1  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
  
Dump of file boost_python3-vc140-mt-1_65.dll  
  
File Type: DLL  
  
  Image has the following dependencies:  
  
    python36.dll  
    MSVCP140.dll  
    KERNEL32.dll  
    VCRUNTIME140.dll  
    api-ms-win-crt-runtime-l1-1-0.dll  
    api-ms-win-crt-heap-l1-1-0.dll  
  
  Summary  
  
        3000 .data  
        1000 .gfids  
        3000 .pdata  
       1B000 .rdata  
        1000 .reloc  
        1000 .rsrc  
       21000 .text  
        1000 .tls  
```

---

## Comment 8

> Username: yoons1  
> Created at: 2017-09-12 17:36:22 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-328927188  

Python27 still dependent in the 1.65.1 pre-built:  
  
dumpbin /dependents boost_python3-vc140-mt-1_65_1.dll  
  
Microsoft (R) COFF/PE Dumper Version 14.00.24215.1  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
Dump of file boost_python3-vc140-mt-1_65_1.dll  
  
File Type: DLL  
  
  Image has the following dependencies:  
  
    python27.dll  
    MSVCP140.dll  
    KERNEL32.dll  
    VCRUNTIME140.dll  
    api-ms-win-crt-runtime-l1-1-0.dll  
    api-ms-win-crt-heap-l1-1-0.dll

---

## Comment 9

> Username: nickdademo  
> Created at: 2017-12-08 09:11:04 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-350212758  

Another update: 1.66 still has this issue.  
  
```  
dumpbin /dependents boost_python3-vc140-mt-x64-1_66.dll  
Microsoft (R) COFF/PE Dumper Version 14.00.24215.1  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
  
Dump of file boost_python3-vc140-mt-x64-1_66.dll  
  
File Type: DLL  
  
  Image has the following dependencies:  
  
    python27.dll  
    MSVCP140.dll  
    KERNEL32.dll  
    VCRUNTIME140.dll  
    api-ms-win-crt-runtime-l1-1-0.dll  
    api-ms-win-crt-heap-l1-1-0.dll  
  
  Summary  
  
        3000 .data  
        1000 .gfids  
        3000 .pdata  
       1B000 .rdata  
        1000 .reloc  
        1000 .rsrc  
       22000 .text  
        1000 .tls  
```

---

## Comment 10

> Username: stefanseefeld  
> Created at: 2017-12-08 14:22:49 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-350274759  

I managed to trace the problem back to its (very likely) cause, and reported it to the Boost.Build ML: https://lists.boost.org/boost-build/2017/12/29715.php  
Let's see what the Boost.Build folks have to say about this.

---

## Comment 11

> Username: prasadghole  
> Created at: 2018-01-23 22:40:41 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-359956280  

It seems if we compare dumpbin output of boost_python-vc141-mt-1_64.dll boost_python3-vc141-mt-1_64.dll both are matching. Means file available on precompiled sourceforge are same.

---

## Comment 12

> Username: stefanseefeld  
> Created at: 2018-01-23 22:48:34 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-359958172  

@prasadghole , indeed. See my last comment (and the link to the related discussion) for explanations.

---

## Comment 13

> Username: stefanseefeld  
> Created at: 2018-04-09 01:28:07 UTC  
> Url: https://github.com/boostorg/python/issues/129#issuecomment-379601751  

The fix is in `develop`, and should find its way into the `1.67` release. Please open a new issue if that's not the case. Thanks !
