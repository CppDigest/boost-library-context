# #32 - Predef always fails on Windows with build-dir directive [Open]

> Username: chambm  
> Created at: 2015-08-07 16:12:12 UTC  
> Updated at: 2021-01-06 14:57:15 UTC  
> Url: https://github.com/boostorg/predef/issues/32  

Hi Rene,  
  
I'm trying to use predef in my Boost.Build project but have run into a strange issue. The check programs like "predef_check_as_cpp.exe" are supposed to return 1 when the check is false, right? I created a test case showing some strange issues on Windows:  
  
Jamroot.jam:  
  
```  
import path ;  
  
path-constant BUILD_PATH : build ;  
  
import predef/check/predef : require check : predef-require predef-check ;  
  
project pwiz   
    : requirements   
        [ predef-check "BOOST_COMP_GNUC >= 4.8" : : <cxxflags>-Wno-unused-local-typedefs ]  
        [ predef-check "BOOST_COMP_GNUC >= 4.9" : : <cxxflags>-fdiagnostics-color=always ]  
  
    # comment out the next line and predef works on Windows; on Linux it works either way  
    : build-dir $(BUILD_PATH)  
    : default-build release  
    ;  
  
exe predef_test : predef_test.cpp ;  
```  
  
On Windows/MSVC-12, that's causing this:  
  
```  
predef.predef_check_action check\predef\bin\msvc-12.0\debug\threading-multi\3567d106897dec7fe92d032e763161c1.txt  
  
    check\predef\bin\msvc-12.0\debug\threading-multi\predef_check_as_cpp.exe check\predef\bin\msvc-12.0\debug\threading-multi\predef_check_as_cpp.pdb "BOOST_COMP_GNUC >= 4.8" > check\predef\bin\msvc-12.0\debug\threading-multi\3567d106897dec7fe92d032e763161c1.txt  
  
...failed predef.predef_check_action check\predef\bin\msvc-12.0\debug\threading-multi\3567d106897dec7fe92d032e763161c1.txt...  
...removing check\predef\bin\msvc-12.0\debug\threading-multi\3567d106897dec7fe92d032e763161c1.txt  
...failed updating 1 target...  
Performing configuration checks  
  
    - BOOST_COMP_GNUC >= 4.8   : no  
...found 1 target...  
...updating 1 target...  
predef.predef_check_action check\predef\bin\msvc-12.0\debug\threading-multi\150e0cfdde3cea25efb86d81e179ac7a.txt  
  
    check\predef\bin\msvc-12.0\debug\threading-multi\predef_check_as_cpp.exe check\predef\bin\msvc-12.0\debug\threading-multi\predef_check_as_cpp.pdb "BOOST_COMP_GNUC >= 4.9" > check\predef\bin\msvc-12.0\debug\threading-multi\150e0cfdde3cea25efb86d81e179ac7a.txt  
  
...failed predef.predef_check_action check\predef\bin\msvc-12.0\debug\threading-multi\150e0cfdde3cea25efb86d81e179ac7a.txt...  
...removing check\predef\bin\msvc-12.0\debug\threading-multi\150e0cfdde3cea25efb86d81e179ac7a.txt  
...failed updating 1 target...  
    - BOOST_COMP_GNUC >= 4.9   : no  
...found 1 target...  
...updating 1 target...  
config-cache.write C:\pwiz-src\trunk\pwiz\predef-test\build-nt-x86_64\project-cache.jam  
...updated 1 target...  
...found 16 targets...  
```  
  
You can see it's building as debug even if I build the rest of the project is building as release, and it's also passing the pdb to the check command. If I edit predef.jam to build as release, then it still fails, but this time it fails because the return code is 1. I don't understand that. The predef.jam code is pretty cryptic relative to my Boost.Build skills, so I'm not sure what's going on. If I get rid of the build-dir directive, it builds fine on Windows (although it's still debug). It builds fine either way on Linux.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2015-09-18 14:11:53 UTC  
> Url: https://github.com/boostorg/predef/issues/32#issuecomment-141461914  

I was unable to repo this with the latest Predef code (develop) on OSX. I'll try on Windows a bit later. But I suspect I wont be able to repo there either. As I rewrote the BBv2 way this works to only rely on compiling the check programs (i.e. doesn't run exes). Perhaps you could try using the current develop state to see if it resolves the issues for you?

---

## Comment 2

> Username: grafikrobot  
> Created at: 2015-10-07 19:21:24 UTC  
> Url: https://github.com/boostorg/predef/issues/32#issuecomment-146301611  

Finally got around to testing on Windows.. And sadly I get an error irrespective of the build-dir option. So still looking into it.

---

## Comment 3

> Username: grafikrobot  
> Created at: 2015-10-15 03:58:37 UTC  
> Url: https://github.com/boostorg/predef/issues/32#issuecomment-148274648  

OK.. And got to do some more testing on Windows, with all code updated.. And everything works for me on Windows.

---

## Comment 4

> Username: chambm  
> Created at: 2015-10-15 17:10:18 UTC  
> Url: https://github.com/boostorg/predef/issues/32#issuecomment-148460722  

I updated my predef-test to the latest master and I still get the error. It still hinges on whether build-dir is specified or not:  
  
```  
compile-c-c++ check\predef\bin\msvc-12.0\debug\threading-multi\predef_check_cc_3567d106897dec7fe92d032e763161c1.obj  
predef_check_cc_as_cpp.cpp  
c:\pwiz-src\trunk\pwiz\predef-test\predef\check\predef_check_cc.h(11) : fatal error C1189: #error :  "FAILED"  
  
    call "C:\Users\chambem2\AppData\Local\Temp\b2_msvc_12.0_vcvarsall_x86.cmd" >nul  
cl /Zm800 -nologo @"check\predef\bin\msvc-12.0\debug\threading-multi\predef_check_cc_3567d106897dec7fe92d032e763161c1.obj.rsp"  
  
...failed compile-c-c++ check\predef\bin\msvc-12.0\debug\threading-multi\predef_check_cc_3567d106897dec7fe92d032e763161c1.obj...  
...failed updating 1 target...  
Performing configuration checks  
  
    - BOOST_COMP_GNUC >= 4.8   : no  
...found 1 target...  
...updating 1 target...  
compile-c-c++ check\predef\bin\msvc-12.0\debug\threading-multi\predef_check_cc_150e0cfdde3cea25efb86d81e179ac7a.obj  
predef_check_cc_as_cpp.cpp  
c:\pwiz-src\trunk\pwiz\predef-test\predef\check\predef_check_cc.h(11) : fatal error C1189: #error :  "FAILED"  
  
    call "C:\Users\chambem2\AppData\Local\Temp\b2_msvc_12.0_vcvarsall_x86.cmd" >nul  
cl /Zm800 -nologo @"check\predef\bin\msvc-12.0\debug\threading-multi\predef_check_cc_150e0cfdde3cea25efb86d81e179ac7a.obj.rsp"  
  
...failed compile-c-c++ check\predef\bin\msvc-12.0\debug\threading-multi\predef_check_cc_150e0cfdde3cea25efb86d81e179ac7a.obj...  
...failed updating 1 target...  
    - BOOST_COMP_GNUC >= 4.9   : no  
...found 1 target...  
...updating 1 target...  
```
