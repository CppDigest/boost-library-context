# #299 - Boost 1.67.0: Visual Studio 15 2017 amd64 b2 error [Closed]

> Username: ruslo  
> Created at: 2018-04-16 06:16:39 UTC  
> Updated at: 2019-03-11 10:20:02 UTC  
> Closed at: 2018-09-04 10:55:29 UTC  
> Url: https://github.com/boostorg/build/issues/299  

I'm experiencing error in build system while trying to build Boost 1.67.0 with Visual Studio 15 2017 amd64.  
  
Steps to reproduce:  
```  
[boost_1_67_0]> bootstrap.bat  
[boost_1_67_0]> b2 -d0 -a link=static threading=multi variant=debug,release --layout=tagged toolset=msvc --user-config=C:/.../boost_1_67_0/boost.user.jam --with-system linkflags=/machine:x64 address-model=64 -j 32 stage --ignore-site-config  
```  
  
Where ``boost.user.jam`` is:  
```  
using msvc  
  :   
  : "C:\...\msvc\2017\VC\Tools\MSVC\14.13.26128\bin\Hostx86\x64\cl.exe"  
  : <archiver>""  
 <ranlib>""  
  <compileflags>/DWIN32  
  <compileflags>/D_WINDOWS  
  <compileflags>/W3  
  <compileflags>/GR  
  <compileflags>/EHsc  
  <compileflags>/DBOOST_ALL_NO_LIB=1  
  <compileflags>/DNOMINMAX  
  <compileflags>/DHAVE_ROUND  
;  
```  
  
Error message:  
```  
C:/.../boost_1_67_0/libs/predef/check/../tools/check\predef.jam:46: Unescaped special character in argument $(language)::$(expression)  
C:/.../boost_1_67_0/tools/build/src/tools\msvc.jam:900: in set-setup-command  
*** argument error  
* rule virtual-target.from-file ( file : file-loc : project )  
* called with: (  : /C:/.../boost_1_57_0 : object(project-target)@129 )  
* missing argument file  
C:/.../boost_1_67_0/tools/build/src/build\virtual-target.jam:970:see definition of rule 'virtual-target.from-file' being called  
C:/.../boost_1_67_0/tools/build/src/tools\msvc.jam:465: in msvc.compile.c++  
C:/.../boost_1_67_0/tools/build/src/kernel\modules.jam:107: in modules.call-in  
C:/.../boost_1_67_0/tools/build/src/util\indirect.jam:105: in indirect.call  
C:/.../boost_1_67_0/tools/build/src/build\virtual-target.jam:902: in execute  
C:/.../boost_1_67_0/tools/build/src/build\virtual-target.jam:821: in class@action.actualize  
C:/.../boost_1_67_0/tools/build/src/build\virtual-target.jam:332: in actualize-action  
C:/.../boost_1_67_0/tools/build/src/build\virtual-target.jam:518: in actualize-no-scanner  
C:/.../boost_1_67_0/tools/build/src/build\virtual-target.jam:142: in class@virtual-target.actualize  
C:/.../boost_1_67_0/tools/build/src/build\configure.jam:258: in try-find-build  
C:/.../boost_1_67_0/tools/build/src/build\configure.jam:396: in find-builds-raw  
C:/.../boost_1_67_0/tools/build/src/build\configure.jam:455: in configure.find-builds  
C:/.../boost_1_57_0\boostcpp.jam:690: in boostcpp.deduce-address-model  
C:/.../boost_1_67_0/tools/build/src/kernel\modules.jam:107: in modules.call-in  
C:/.../boost_1_67_0/tools/build/src/util\indirect.jam:105: in indirect.call  
C:/.../boost_1_67_0/tools/build/src/build\property.jam:144: in property.evaluate-conditionals-in-context  
C:/.../boost_1_67_0/tools/build/src/build\targets.jam:1087: in evaluate-requirements  
C:/.../boost_1_67_0/tools/build/src/build\targets.jam:1121: in common-properties2  
C:/.../boost_1_67_0/tools/build/src/build\targets.jam:1017: in targets.common-properties  
C:/.../boost_1_67_0/tools/build/src/build\targets.jam:1313: in alias-target-class.generate  
C:/.../boost_1_57_0\boostcpp.jam:500: in build-multiple  
C:/.../boost_1_57_0\boostcpp.jam:490: in class@top-level-target.generate  
C:/.../boost_1_67_0/tools/build/src/build\targets.jam:812: in generate-really  
C:/.../boost_1_67_0/tools/build/src/build\targets.jam:784: in class@main-target.generate  
C:/.../boost_1_67_0/tools/build/src\build-system.jam:797: in load  
C:\...\boost_1_67_0\tools\build\src/kernel\modules.jam:295: in import  
C:\...\boost_1_67_0\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
C:\...\boost_1_67_0\boost-build.jam:17: in module scope  
```  
  
Regression note: Same configuration worked fine with Boost 1.66.0

---

## Comment 1

> Username: JasonDictos  
> Created at: 2018-05-30 23:36:23 UTC  
> Url: https://github.com/boostorg/build/issues/299#issuecomment-393353021  

Any news on this?

---

## Comment 2

> Username: xsacha  
> Created at: 2018-07-17 00:25:45 UTC  
> Updated at: 2018-07-18 01:16:37 UTC  
> Url: https://github.com/boostorg/build/issues/299#issuecomment-405421029  

Any chance of a fix before 1.68 is released?  
  
From what I've been able to figure out from: https://groups.google.com/forum/#!topic/boost-developers-archive/GWLw-YL24ZU  
  
> The unescaped character warnings are new to current develop Boost.Build  
  
That's how it started.  
  
>  They're harmless for now, but will break  
> at some unspecified time in the future, when  
> I switch to the new lexer.  
  
That's now.  
  
> The full list is ":;{}=+?*()<>" (some of which  
> are context sensitive)  In general, the easiest  
> fix is to quote the whole token.  I'll see about  
> adjusting the message.  
  
Can't see any of these in the wrong place.  
Is it perhaps this line? `<compileflags>/DBOOST_ALL_NO_LIB=1` where the equals needs to be quoted?

---

## Comment 3

> Username: xsacha  
> Created at: 2018-08-28 06:09:41 UTC  
> Updated at: 2018-08-28 06:34:36 UTC  
> Url: https://github.com/boostorg/build/issues/299#issuecomment-416462861  

A note that it also fails on some propogated CXX flags such as:  
Boost\__thread\Build\boost.user.jam:6: Unescaped special character in argument <compileflags>/arch:AVX  
  
Naturally, I attempted quoting every cxx flag, which looks like this:  
```  
using msvc  
  :   
  : "C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.14.26428\bin\Hostx64\x64\cl.exe"  
  : <archiver>""  
 <ranlib>""  
  <compileflags>"/arch:AVX"  
  <compileflags>"/DBOOST_ALL_NO_LIB=1"  
  <compileflags>"/DNOMINMAX"  
  <compileflags>"/DHAVE_ROUND"  
;  
```  
  
This appears to have fixed that error I got, but then I arrived back at the original issue:  
  
> C:/.hunter/_Base/f58bcf2/ccf35b3/37fc4c8/Build/Boost/__thread/Source/tools/build/src/tools\msvc.jam:1087: in set-setup-command  
>   *** argument error  
>   * rule virtual-target.from-file ( file : file-loc : project )  
>   * called with: (  : /C:/.hunter/_Base/f58bcf2/ccf35b3/37fc4c8/Build/Boost/__thread/Source : object(project-target)@129 )  
>   * missing argument file  
>   C:/.hunter/_Base/f58bcf2/ccf35b3/37fc4c8/Build/Boost/__thread/Source/tools/build/src/build\virtual-target.jam:970:see definition of rule 'virtual-target.from-file' being called  
  
  
Edit: Possibly fixed by https://github.com/boostorg/build/commit/d11e3e4ef342b7723b3cf554fde7677873c47040

---

## Comment 4

> Username: daminetreg  
> Created at: 2018-09-02 08:52:24 UTC  
> Url: https://github.com/boostorg/build/issues/299#issuecomment-417914915  

I confirm it is fixed with d11e3e4 I was experiencing the same issue on the same toolset.  
  
@ruslo I patched boost 1.68.0 and hunter to add the @swatanabe's patch in https://github.com/nxxm/hunter/tree/feature/fix-boost-1.68.0-msvc .  
  
I'll make a PR to ruslo/hunter as soon as I get appveyor and travis to build the branches for all the toolsets. For info on my own CI which tests only 3 toolsets in this case it works on macOS, ubuntu, win10.

---

## Comment 5

> Username: ruslo  
> Created at: 2018-09-04 10:55:29 UTC  
> Url: https://github.com/boostorg/build/issues/299#issuecomment-418324453  

@swatanabe Thanks for the https://github.com/boostorg/build/commit/d11e3e4ef342b7723b3cf554fde7677873c47040 fix.  
  
@daminetreg Thanks for the https://github.com/hunter-packages/boost/pull/5 and https://github.com/ruslo/hunter/pull/1533 pull requests.  
  
Visual Studio 15 2017 build works fine now, closing.

---

## Comment 6

> Username: michaelmaguire  
> Created at: 2018-09-07 13:05:58 UTC  
> Updated at: 2018-09-07 13:24:40 UTC  
> Url: https://github.com/boostorg/build/issues/299#issuecomment-419433128  

I see that the Visual Studio build fix was patched 5 days ago (September 2nd), but from https://www.boost.org/users/history/ 1.68.0 was released last month (August 9th, 2018 03:46 GMT).   
  
Is there a plan to do a 1.68.1 patch release of the sources, e.g. like https://dl.bintray.com/boostorg/release/1.65.1/source/ ?

---

## Comment 7

> Username: michaelmaguire  
> Created at: 2019-03-11 10:20:02 UTC  
> Url: https://github.com/boostorg/build/issues/299#issuecomment-471482269  

Looks like this issue is now fixed with boost 1.69.0
