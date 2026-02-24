# #244 - fails to link libboost_python [Closed]

> Username: wwestlake  
> Created at: 2018-12-15 23:02:34 UTC  
> Updated at: 2018-12-18 19:38:11 UTC  
> Closed at: 2018-12-18 19:38:11 UTC  
> Url: https://github.com/boostorg/python/issues/244  

LINK : fatal error LNK1104: cannot open file 'boost_python36-vc141-mt-gd-x64-1_70.lib'  
  
The build system produces file: 'libboost_python36-vc141-mt-gd-x64-1_70.lib'  
  
in file boost/python/detail/config.hpp line 110 need to change from:  
  
#define BOOST_LIB_NAME BOOST_PYTHON_CONCAT(boost_python, PY_MAJOR_VERSION, PY_MINOR_VERSION)  
  
to  
  
#define BOOST_LIB_NAME BOOST_PYTHON_CONCAT(libboost_python, PY_MAJOR_VERSION, PY_MINOR_VERSION)

---

## Comment 1

> Username: StevenBaby  
> Created at: 2018-12-18 11:08:42 UTC  
> Url: https://github.com/boostorg/python/issues/244#issuecomment-448184426  

maybe you need build boost_python or download boost libraries at <https://sourceforge.net/projects/boost/files/boost-binaries/1.69.0/>  
  
and add those lib files in additional library

---

## Comment 2

> Username: wwestlake  
> Created at: 2018-12-18 12:10:49 UTC  
> Updated at: 2018-12-18 12:13:11 UTC  
> Url: https://github.com/boostorg/python/issues/244#issuecomment-448199663  

I did build boost python.  The build system produces the library names libboost_pythonVERSIONINFO.lib.  But in the config.h it is call boost_pythonVERSIONINFO.lib.  That simple change allowed it to link, without the change it doesn't link directly after the build.  Also, the prebuilt binaries are also called libboost_pythonXXX.lib not boost_pythonXXX.lib

---

## Comment 3

> Username: StevenBaby  
> Created at: 2018-12-18 12:15:02 UTC  
> Url: https://github.com/boostorg/python/issues/244#issuecomment-448200724  

did you try rename libboost_pythonVERSIONINFO.lib to libboost_pythonXXX.lib

---

## Comment 4

> Username: wwestlake  
> Created at: 2018-12-18 13:14:03 UTC  
> Updated at: 2018-12-18 13:15:41 UTC  
> Url: https://github.com/boostorg/python/issues/244#issuecomment-448216693  

Ok, those were just placeholders.  The important part is the preceding "lib".  Specifically the file produced in the output directory is called: libboost_python36-vc141-mt-gd-x64-1_70.lib, note that it begins with lib, but the config calls it: boost_python36-vc141-mt-gd-x64-1_70.lib after the macro expansion.  Note that the config does not start with lib, but the file does.  This is why it doesn't link.  By placing the lib at the front of the file name in the config you get the correct file name it it all links with no issues.  With the file name called: boost_python36-vc141-mt-gd-x64-1_70.lib in the file name I get the following linker error:  LINK : fatal error LNK1104: cannot open file 'boost_python36-vc141-mt-gd-x64-1_70.lib' because that file does not exist, it is called 'libboost_python36-vc141-mt-gd-x64-1_70.lib'  
  
By adding the lib prefix to the name of the file in tthe config.h it all works fine.  
  
Bill

---

## Comment 5

> Username: stefanseefeld  
> Created at: 2018-12-18 14:26:31 UTC  
> Updated at: 2018-12-18 14:47:33 UTC  
> Url: https://github.com/boostorg/python/issues/244#issuecomment-448238152  

Disclaimer: I'm not a Windows user, so this is just observation:  
  
Boost allows you to build shared or static libraries. The former use the `"lib<libname>.lib"` form, the latter `"<libname>.dll"` for the library, and `"<libname>.lib"` for the import library (https://en.wikipedia.org/wiki/Dynamic-link_library#Import_libraries).  
  
So, it looks like you are getting confused as you mistake a static library (with the `lib` prefix) for an import library (no `lib` prefix).  
Make sure you use the correct compile and link options: auto-linking (the MSVC feature that uses the macros you claim produce the wrong names) works with shared libs, and thus require the un-prefixed library names.

---

## Comment 6

> Username: wwestlake  
> Created at: 2018-12-18 19:37:28 UTC  
> Url: https://github.com/boostorg/python/issues/244#issuecomment-448343319  

You are correct, that is the actual issue.  Thanks, I will close the issue.  
  
On Tue, Dec 18, 2018 at 9:26 AM Stefan Seefeld <notifications@github.com>  
wrote:  
  
> Disclaimer: I'm not a Windows user, so this is just observation:  
>  
> Boost allows you to build shared or static libraries. The former use the  
> "lib.lib" form, the latter ".dll" for the library, and ".lib" for the  
> import library (  
> https://en.wikipedia.org/wiki/Dynamic-link_library#Import_libraries).  
>  
> So, it looks like you are getting confused as you mistake a static library  
> (with the lib prefix) for an import library (no lib prefix).  
> Make sure you use the correct compile and link options: auto-linking (the  
> MSVC feature that uses the macros you claim produce the wrong names) works  
> with shared libs, and thus require the un-prefixed library names.  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/python/issues/244#issuecomment-448238152>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/ADBc_8vlPOk18JxvSgSR3bVpGcy-ldGxks5u6PsYgaJpZM4ZU70Q>  
> .  
>  
  
  
--   
Bill Westlake  
(586) 405-4533  
wwestlake@lagdaemon.com  
Server: minecraft.lagdaemon.com:25765  
(Whitelisted, contact me first)

---

## Comment 7

> Username: wwestlake  
> Created at: 2018-12-18 19:38:11 UTC  
> Url: https://github.com/boostorg/python/issues/244#issuecomment-448343547  

solved it was a build setting issue
