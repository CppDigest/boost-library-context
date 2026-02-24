# #119 - bjam build error using Intel C/C++ Compiler on Windows [Closed]

> Username: sav-ix  
> Created at: 2016-01-19 21:03:45 UTC  
> Updated at: 2017-07-15 12:04:28 UTC  
> Closed at: 2017-07-15 12:04:28 UTC  
> Url: https://github.com/boostorg/build/issues/119  

Hello, everyone,  
  
For `bjam` using ICC on Windows got error:  
  
```  
bootstrap intel-win32  
  
<snip>  
  
Failed to build Boost.Build engine.  
Please consult bootstrap.log for further diagnostics.  
  
You can try to obtain a prebuilt binary from  
  
   http://sf.net/project/showfiles.php?group_id=7586&package_id=72941  
  
Also, you can file an issue at http://svn.boost.org  
Please attach bootstrap.log in that case.  
```  
  
Full log added in [attachment](https://github.com/boostorg/build/files/96520/bootstrap.zip).  
  
The source of error is symbol `-`. If in files:  
  
```  
<BOOST sources>\tools\build\src\engine\build.bat  
<BOOST sources>\tools\build\src\engine\build.jam  
```  
  
remove it from string `intel-win32`, `bjam` would be successfully built using command `bootstrap intelwin32`. The drawback of such solution, that there are other files, which refer `intel-win32` key:  
  
```  
<BOOST sources>\boost\config\compiler\intel.hpp  
<BOOST sources>\doc\html\jam.html  
<BOOST sources>\doc\html\jam\history.html  
<BOOST sources>\status\expected_results.xml  
<BOOST sources>\status\explicit-failures-markup.xml  
<BOOST sources>\tools\build\doc\bjam.qbk  
<BOOST sources>\tools\build\doc\history.qbk  
<BOOST sources>\tools\build\src\engine\boehm_gc\doc\README.Mac  
<BOOST sources>\tools\build\src\tools\intel-win.jam  
```  
  
@eas provided a fix for this issue (see https://github.com/eas/build/commit/fdbef0e3f0083979bfb6cfa7cdfb37b44eb400b0), but for some reason it didn't appear in Boost.build master. Thus added an appropriate PR https://github.com/boostorg/build/pull/211.  
  
Environment:  
- Windows 10 x64,  
- Intel Parallel Studio XE 2017 Update 4,  
- Visual Studio 2015 Update 3,  
- BOOST-1.64.0.  
  
  
Alexander
