# #697 - Intel has new compiler, but still supports old one [Open]

> Username: eldiener  
> Created at: 2020-12-30 15:38:15 UTC  
> Updated at: 2021-05-29 16:22:51 UTC  
> Url: https://github.com/boostorg/build/issues/697  

Intel's new compiler technology is called oneAPI. The new technology still supports the old compiler, but includes two parallel processing compilers, icx/icpx and dpcpp, based on clang. This bodes well for Intel customers as their previous compiler, although good, was not of the quality of clang. Boost Build should probably try to support these two compilers somewhere down the road.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-12-30 15:44:50 UTC  
> Url: https://github.com/boostorg/build/issues/697#issuecomment-752667577  

This is definitely on my to do list. One important design question is whether to: make a new fresh toolset, adjust the existing intel toolset, or make a clang variant toolset. I don't know what the front end flags are like, as I haven't looked at it yet. But will eventually find out when I get to look at it.

---

## Comment 2

> Username: eldiener  
> Created at: 2021-01-06 20:10:55 UTC  
> Url: https://github.com/boostorg/build/issues/697#issuecomment-755620593  

That is great !   
  
BTW Intel has decide that all those people who previously had free open source developer licenses for their previous compiler implementations, such as I had for Windows and Linux for many releases, would no longer be renewed. This makes testing of any previous implementation impossible as the compiler now tells me that my license has expired and all compilation immediately fails. Of course the oneAPI is free for everybody without needing a license, and there is a version of the previous compiler technology within it which should still work with the Boost.Build intel toolsets, so maybe I should not complain <g>.

---

## Comment 3

> Username: simmse  
> Created at: 2021-01-28 23:43:41 UTC  
> Url: https://github.com/boostorg/build/issues/697#issuecomment-769472315  

Hey Guys,  
  
Using the oneAPI tool set with the HPC kit installed, b2 does not find the icl.exe.  This is with the 1.75.0 Boost.  I added the ICPP_COMPILER19 environment variable to the command window, as well as the Windows computer system.  All variations of the environment variable continues to generate all "no" or "none" status on the initial configuration checks.  I also modified the intel-win.jam to correctly find the matching toolset contents to the version numbers between Intel and Visual Studio.  I also modified the .icl-target-subdirectories content.  No combination successfully has b2 finding the icl.exe.  The version number with the oneAPI icl.exe is 19.2.    
  
toolset="intel-19.2-vc14.2"  
  
**intel-win.jam additions**  
.iclvars-19.2-supported-vcs = "14.2 14.1" ;  
.iclvars-version-alias-vc14.2 = vs2019 ;  
.icl-target-subdirectories = intel64 intel64_ia32 ia32 ;  
  
The command line used in the oneAPI prompt in run as Administrator mode:  
  
b2 --build-dir=C:\BoostObjx64 --libdir="C:\Boost\lib" architecture=x86 address-model=64 toolset="intel-19.2-vc14.2" --build-type=complete install -j36   
  
Any guidance is greatly appreciated.

---

## Comment 4

> Username: eldiener  
> Created at: 2021-01-30 18:23:29 UTC  
> Updated at: 2021-01-30 18:24:47 UTC  
> Url: https://github.com/boostorg/build/issues/697#issuecomment-770256199  

Please show your toolset definition for Intel on Windows. Mine is:  
  
```  
using intel : 21.1 :   
    :  
    <compatibility>vc14.2   
    <root>"C:/Utilities/Intel/oneAPI"  
    <rewrite-setup-scripts>off   
    <cxxflags>"/Qdiag-disable:2415,367,3280,177"  
    <cxxflags>"/Zc:__cplusplus"  
    ;  
  
```  
For the bjam command line I use 'toolset=intel-21.1" for the toolset parameter among other parameters, and everything works fine. I admit I had not added anything to intel-win.jam for 19.2 because when I had first downloaded the oneAPI the version was already 21.1. While your first addition is valid the other two are already in the intel-win.jam file.

---

## Comment 5

> Username: simmse  
> Created at: 2021-02-02 00:51:49 UTC  
> Updated at: 2021-02-02 00:54:36 UTC  
> Url: https://github.com/boostorg/build/issues/697#issuecomment-771266992  

The version displayed for icx.exe and icl.exe, at the Intel oneAPI Tools command line, have a variation of 2021.  
`icx.exe = Version 2021.1 Build 20201113`  
or  
`icl.exe = Version 2021.1 Build 20201112_000000`  
  
Inside Visual Studio, the version number is 19.2 for the icl.exe.  The displayed string in the Solution Explorer says  
`Intel C++ 19.2` to the right of the project name.  To set to the icl.exe quickly, make the selection in the Solution Explorer via a right mouse button click, then navigate to `Intel Compiler | Use Intel C++ Compiler Classic`.    
  
Inside intel-win.jam, here is what continues to fail to find icl.exe at the oneAPI command prompt, x64 Visual Studio 2019:  
`using intel-win : 19.2 : : <cxxflags>/EHsc /fp\:precise /fp\:source /Qstd=c++17 /DBOOST_FILESYSTEM_NO_DEPRECATED /Qlong_double <cflags>/fp\:precise /fp\:source /Qstd=c11 /DBOOST_FILESYSTEM_NO_DEPRECATED /Qlong_double ;  `    
  
Inside intel-win.jam, here is what finds icl.exe correctly from the same oneAPI command prompt:  
```  
using intel : 21.1 :  
    :  
    <compatibility>vc14.2  
    <root>"C:/iOrt"  
    <rewrite-setup-scripts>off  
    <cxxflags>"/EHsc /fp:precise /fp:source /Qstd=c++17 /DBOOST_FILESYSTEM_NO_DEPRECATED /Qlong-double"  
    <cflags>"/fp:precise /fp:source /Qstd=c11 /DBOOST_FILESYSTEM_NO_DEPRECATED /Qlong-double"  
    ;  
```  
Note that the root value is a shortened link to `C:\Program Files (x86)\Intel\oneAPI`  
I did have to add the supported line for 21.1 inside intel-win.jam  
`.iclvars-21.1-supported-vcs = "14.2 14.1" ;`  
  
I used `toolset="intel-21.1"`.  I also modified the intel-win.jam setup from using iclvars.bat to using setvars.bat.  I have the same number of Boost 1.75.0 output files as 1.72.0 builds via b2.  The older 1.72.0 build uses the Intel 19.0, Update Five compiler tool set.  The total output file quantity is 296.  Does that look correct?  With a Microsoft only b2 build, the total is 359.  Thank you for your configuration details.  That solved the issue.  Is there a setting within the jam files that looks for 21.1 instead of 19.2?  
  
There is a related question.  What is the recommended platform (e.g. github/boostorg, Microsoft developer community, Boost mailing list, etc.) to ask about Boost Test, integration with the Visual Studio 2019 Test Explorer, and compiling with Microsoft versus a different compiler?  Naturally, the Intel unit tests correctly appear.  However, the Microsoft unit tests' display are inconsistent.  Most of the time the debug builds show no unit tests within the Visual Studio Test Explorer.  It is the same source code used for the Intel within Visual Studio build.  However, the Microsoft release builds inside Visual Studio and the Boost Testing framework build output files from the oneAPI command line, do show the unit tests after building within Visual Studio.  By an unexpected outcome, the unit tests started to add in the Test Explorer.  Does any of this Visual Studio user interface behavior sound familiar?    
  
Thanks again for the configuration details.  They are greatly appreciated.

---

## Comment 6

> Username: eldiener  
> Created at: 2021-02-02 06:34:38 UTC  
> Url: https://github.com/boostorg/build/issues/697#issuecomment-771401966  

I do not think you have the latest intel-win.jam. In the latest intel-win.jam line 521 and line 522 reads:  
  
`.iclvars-21.1-supported-vcs = "14.2 14.1" ;`  
`.iclvars-2021.1-supported-vcs = "14.2 14.1" ;`  
  
This I added to support the oneAPI 2021.1 version, which can also be used as the oneAPI 21.1 version in intel-win.jam. So when you say that:  
  
"I did have to add the supported line for 21.1 inside intel-win.jam":  
  
`.iclvars-21.1-supported-vcs = "14.2 14.1" ;`  
  
if you had the latest version you would see that it is already there.   
  
You can configure your toolset to start with:  
  
`using intel : 2021.1 : `  
  
or   
  
`using intel : 21.1 : `  
  
I would pay not attention to what the version number for icl.exe is in Visual Studio. As you found out, when using the command line, the version number is 2021.1, which is also supported in intel-win.jam as 21.1. I am glad you figured out how to get it to work for you from my toolset example. But before you think you need to make changes to intel-win.jam you should really try to get the latest changes to the file which I made.

---

## Comment 7

> Username: simmse  
> Created at: 2021-02-02 16:12:03 UTC  
> Url: https://github.com/boostorg/build/issues/697#issuecomment-771748849  

The process is to obtain and use the current version.  The 1.75.0 Boost release set does not contain the oneAPI modifications.  When reviewing the build and developers mailing lists, posts relating to this do not appear.  Sorry about not searching the repository.  For future b2 and Intel issues, the intel-win.jam history will be closely reviewed.     
  
Building commercial software with the Intel oneAPI has been a time consuming project.  Both Visual Studio and Intel updates occur simultaneously last December.  Solving the b2 & Intel problem makes it all worthwhile.  Thank you very much for your help.

---

## Comment 8

> Username: eldiener  
> Created at: 2021-02-05 20:41:53 UTC  
> Updated at: 2021-02-05 20:42:50 UTC  
> Url: https://github.com/boostorg/build/issues/697#issuecomment-774277590  

What I was trying to tell you is that as far as using the icl.exe, which is part of the oneAPI distribution, on Windows there is already an updated intel-win.jam in the 'develop' branch which supports your use case. This does not mean that there is support for the oneAPI parallel processing icx.exe or dpcpp.exe compilers as yet, which I believe Rene is working toward.

---

## Comment 9

> Username: simmse  
> Created at: 2021-02-08 15:46:31 UTC  
> Url: https://github.com/boostorg/build/issues/697#issuecomment-775241893  

The same conclusion has been reached here.  It is not clear when the icx.exe and/or dpcpp.exe will be used for production project compilations.

---

## Comment 10

> Username: tanzislam  
> Created at: 2021-04-05 19:48:38 UTC  
> Updated at: 2021-04-05 23:05:02 UTC  
> Url: https://github.com/boostorg/build/issues/697#issuecomment-813610742  

The flags of the [new compiler](https://software.intel.com/content/www/us/en/develop/documentation/oneapi-dpcpp-cpp-compiler-dev-guide-and-reference/top/compiler-reference/compiler-options/alphabetical-list-of-compiler-options.html) versus the [classic compiler](https://software.intel.com/content/www/us/en/develop/documentation/cpp-compiler-developer-guide-and-reference/top/compiler-reference/compiler-options/alphabetical-list-of-compiler-options.html) seem similar enough to me. :)  
  
In my Intel oneAPI Base Toolkit installation there is no `icl.exe`, so I had to change [line 316 of `intel-win.jam`](https://github.com/boostorg/build/blob/develop/src/tools/intel-win.jam#L316) to:  
  
```diff  
-        toolset.flags intel-win.compile .CC $(cpu-conditions) : icl ;  
+        toolset.flags intel-win.compile .CC $(cpu-conditions) : $(command) ;  
```  
  
I also needed to change [line 200 of `auto_link.hpp`](https://github.com/boostorg/config/blob/develop/include/boost/config/auto_link.hpp#L200) to:  
  
```diff  
-#  elif defined(__ICL)  
+#  elif defined(__ICL) || defined(__INTEL_LLVM_COMPILER)  
```  
  
Afterwards I managed to use the new compiler (`icx.exe`) with this `user-config.jam`:  
  
```  
using intel : icx~2021.2 : icx.exe : <root>"C:\\Program Files (x86)\\Intel\\oneAPI" <compatibility>vc14.2 ;  
```  
  
and run this in a **Intel oneAPI command prompt for IA32 for Visual Studio 2019** window successfully (with the `develop` branch checked out):  
  
```cmd  
set CXX=icx.exe  
set "B2_CXX_LINK=/link advapi32.lib user32.lib"  
bootstrap.bat intel-win32  
del project-config.jam  
b2 headers  
b2 --layout=versioned toolset=intel variant=debug threading=multi runtime-link=static address-model=32 architecture=x86 --with-date_time  
```

---

## Comment 11

> Username: eldiener  
> Created at: 2021-04-10 07:31:16 UTC  
> Url: https://github.com/boostorg/build/issues/697#issuecomment-817093917  

I admit I thought the icx compiler uses clang flags and the icl compiler uses Visual Studio like flags. However if this is not so your suggested fixes sound great and I will test them.

---

## Comment 12

> Username: stale[bot]  
> Created at: 2021-05-29 16:22:21 UTC  
> Url: https://github.com/boostorg/build/issues/697#issuecomment-850859585  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
