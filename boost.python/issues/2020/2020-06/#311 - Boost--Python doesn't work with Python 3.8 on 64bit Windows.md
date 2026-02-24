# #311 - Boost::Python doesn't work with Python 3.8 on 64bit Windows. [Open]

> Username: SPKorhonen  
> Created at: 2020-06-08 14:35:32 UTC  
> Updated at: 2021-03-02 19:16:08 UTC  
> Url: https://github.com/boostorg/python/issues/311  

I seem to be unable to get boost::python working for CPython 3.8 (official 3.8.3 release for windows 64 bit) with boost 1.73.0. I'm using Visual Studio 2017 (15.9.23).  
  
Problem is that boost and all extensions compile without error but any and all extensions using boost:::python fail to load. The exact error is "ImportError: DLL load failed while importing ben_scott1_ext: The specified module could not be found." which is not particularly helpful. According to dependency walker no dependencies seem to be missing. Interestingly enough the same extensions work fine on CPython 3.7 (and earlier),  
  
Any idea on how to diagnose this further?  
  
Python version:  
    Version      : 3.8.3  
    Compiler   : MSC v.1924 64 bit (AMD64)  
    Build         : ('tags/v3.8.3:6f8c832', 'May 13 2020 22:37:02')

---

## Comment 1

> Username: SPKorhonen  
> Created at: 2020-06-08 20:00:44 UTC  
> Url: https://github.com/boostorg/python/issues/311#issuecomment-640855455  

Disregard... I was building with wrong version of Visual Studio. It seems that CPython 3.8 uses Visual Studio 2019 on Windows.

---

## Comment 2

> Username: SPKorhonen  
> Created at: 2020-06-08 21:08:03 UTC  
> Url: https://github.com/boostorg/python/issues/311#issuecomment-640887465  

Unfortunately compiling with VS 2019 did not resolve this issue. Import of any *.pyd still fails with ImportError

---

## Comment 3

> Username: SPKorhonen  
> Created at: 2020-06-08 22:34:34 UTC  
> Url: https://github.com/boostorg/python/issues/311#issuecomment-640922791  

With debug build of CPython I get the following message:  
  
ImportError: cannot import name 'NMRSpectrumToolsPython' from partially initialized module 'NMRSpectrumTools' (most likely due to a circular import)

---

## Comment 4

> Username: SPKorhonen  
> Created at: 2020-06-09 10:24:52 UTC  
> Url: https://github.com/boostorg/python/issues/311#issuecomment-641195465  

@stefanseefeld Any idea on how to debug this?  
  
It seems that under Windows 10 boost::python is incompatible with Python 3.8. Boost 1.73.0 works with Python 3.7 and tests pass, whereas for Python 3.8 quite literally nothing works.

---

## Comment 5

> Username: nirgoren  
> Created at: 2021-02-25 11:50:10 UTC  
> Updated at: 2021-02-25 12:21:39 UTC  
> Url: https://github.com/boostorg/python/issues/311#issuecomment-785838516  

Had a similar issue with 3.8/3.9, you can try building the boost python library yourself (bootstrap then b2 at the root directory of boost) and linking to it instead of linking to the provided pre-built library that comes with the installer for Windows - it worked for me.  
  
Edit: This is most likely not the issue. I just linked statically this time so it was not brought up, but the issue is most likely related to this change:  
https://docs.python.org/3/whatsnew/3.8.html#bpo-36085-whatsnew  
  
So you can no longer just rely on the boost python dll (or any other dll your pyd file needs to dynamically link with) being in your system path.

---

## Comment 6

> Username: stefanseefeld  
> Created at: 2021-02-25 12:12:56 UTC  
> Url: https://github.com/boostorg/python/issues/311#issuecomment-785851584  

Yes, I suspect the problem is really with the way the Windows binary packages are generated, so this should be brought up on the Boost mailing list.

---

## Comment 7

> Username: SPKorhonen  
> Created at: 2021-02-27 11:24:07 UTC  
> Url: https://github.com/boostorg/python/issues/311#issuecomment-787058065  

In my specific case I'm building boost from sources so the problem could not be related to pre-build binaries.  
  
I've tried to debug the problem and it seems to center around module initialization failing for unspecified reason and could very well be caused by the change in dll resolution linked by @nirgoren. When I have a spare moment I'll add relevant calls to my module intialization and see if that helps.

---

## Comment 8

> Username: SPKorhonen  
> Created at: 2021-03-02 19:16:08 UTC  
> Url: https://github.com/boostorg/python/issues/311#issuecomment-789148361  

Big thanks to @nirgoren! When I added appropriate paths via os.add_dll_directory things started to work once again. So this issue has been resolved.   
  
I wonder if we should add a usage note to boost::python documentation to warn users that they need to manually add the paths if they are using dynamic linking on Windows.  
  
Personally I think a change like the one to dll-resolution should be marked as critical breaking change in Python as this means that most non-monolithic extension modules will break on Windows without an easy way to fix the issue outside of adding system path via os.add_dll_directory which defeats the purpose of the change anyways.
