# #230 - boost 1.65 / vc14: missing argument location [Closed]

> Username: jschueller  
> Created at: 2017-08-22 18:32:35 UTC  
> Updated at: 2017-09-06 12:08:29 UTC  
> Closed at: 2017-09-06 12:08:29 UTC  
> Url: https://github.com/boostorg/build/issues/230  

When updating our boost recipe from 1.64 to 1.65, building with vc14, we got this error:  
```  
[COPY] bin.ntx86\bjam.exe  
...updated 3 targets...  
(C:\bld\boost-cpp_1503418189060\_b_env) C:\bld\boost-cpp_1503418189060\work\boost_1_65_0\tools\build\src\engine>exit /b 0   
C:/bld/boost-cpp_1503418189060/work/boost_1_65_0/tools/build/src/tools\msvc.jam:1266: in default-path  
*** argument error  
* rule path.exists ( location )  
* called with: (  )  
* missing argument location  
(builtin):see definition of rule 'path.exists' being called  
C:/bld/boost-cpp_1503418189060/work/boost_1_65_0/tools/build/src/tools\msvc.jam:735: in auto-detect-toolset-versions  
C:/bld/boost-cpp_1503418189060/work/boost_1_65_0/tools/build/src/tools\msvc.jam:1710: in load  
C:\bld\boost-cpp_1503418189060\work\boost_1_65_0\tools\build\src/kernel\modules.jam:295: in import  
C:/bld/boost-cpp_1503418189060/work/boost_1_65_0/tools/build/src/build\toolset.jam:41: in toolset.using  
C:/bld/boost-cpp_1503418189060/work/boost_1_65_0/tools/build/src/build\project.jam:1052: in using  
project-config.jam:3: in modules.load  
C:/bld/boost-cpp_1503418189060/work/boost_1_65_0/tools/build/src\build-system.jam:249: in load-config  
C:/bld/boost-cpp_1503418189060/work/boost_1_65_0/tools/build/src\build-system.jam:412: in load-configuration-files  
C:/bld/boost-cpp_1503418189060/work/boost_1_65_0/tools/build/src\build-system.jam:524: in load  
C:\bld\boost-cpp_1503418189060\work\boost_1_65_0\tools\build\src/kernel\modules.jam:295: in import  
C:\bld\boost-cpp_1503418189060\work\boost_1_65_0\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
C:\bld\boost-cpp_1503418189060\work\boost_1_65_0\boost-build.jam:17: in module scope  
```  
  
here is our script:  
https://github.com/conda-forge/boost-cpp-feedstock/blob/master/recipe/bld.bat  
  
  
see complete log:  
https://ci.appveyor.com/project/conda-forge/boost-cpp-feedstock/build/1.0.32/job/qh6s60f3aggfxk2t

---

## Comment 1

> Username: SSE4  
> Created at: 2017-09-05 07:48:44 UTC  
> Url: https://github.com/boostorg/build/issues/230#issuecomment-327098682  

@jschueller I've faced the same issue. the problem is that python programs upper-cases environment variables, so "ProgramFiles" become "PROGRAMFILES", and b2 fails to locate them

---

## Comment 2

> Username: jschueller  
> Created at: 2017-09-05 11:43:58 UTC  
> Url: https://github.com/boostorg/build/issues/230#issuecomment-327151289  

@SSE4 where exactly does this happen ?

---

## Comment 3

> Username: grafikrobot  
> Created at: 2017-09-05 15:59:04 UTC  
> Url: https://github.com/boostorg/build/issues/230#issuecomment-327221772  

@jschueller This is an issue specific to how Python deals with environment variables. It upper-cases env vars names when it imports them. And when it exports them back out (for running subprocesses) it does it with the upper-case names instead of using the original cased-names. I'll try and see if there's a work around for this one variable I can implement for the next b2/boost release.

---

## Comment 4

> Username: jschueller  
> Created at: 2017-09-05 16:20:00 UTC  
> Url: https://github.com/boostorg/build/issues/230#issuecomment-327227969  

my question was more: what changed in boost between 1.64 and 1.65 for this to break?

---

## Comment 5

> Username: grafikrobot  
> Created at: 2017-09-05 17:54:01 UTC  
> Url: https://github.com/boostorg/build/issues/230#issuecomment-327254509  

@jschueller The key change is that 1.65 added msvc-14.1 detection using the vswhere program. Which uses the ProgramFiles env var to locate the vswhere program itself.  
  
FYI.. I've now fixed this problem on develop branch: 9821622630035c0d7c3f4d7c196f847a335da2b6

---

## Comment 6

> Username: jschueller  
> Created at: 2017-09-05 18:44:00 UTC  
> Updated at: 2017-09-05 18:56:51 UTC  
> Url: https://github.com/boostorg/build/issues/230#issuecomment-327266748  

ok, thanks a bunch, I'll try to apply this to see if it fixes my builds

---

## Comment 7

> Username: jschueller  
> Created at: 2017-09-06 06:38:36 UTC  
> Url: https://github.com/boostorg/build/issues/230#issuecomment-327389645  

it is fixed!
