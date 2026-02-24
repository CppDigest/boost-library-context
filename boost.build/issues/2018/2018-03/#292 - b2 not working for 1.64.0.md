# #292 - b2 not working for 1.64.0 [Open]

> Username: rpitre  
> Created at: 2018-03-21 22:31:09 UTC  
> Updated at: 2021-06-26 03:10:17 UTC  
> Url: https://github.com/boostorg/build/issues/292  

boostrap.bat works without problem: the engine and b2 compiles successfully using vc14.  
But when running b2 I get those errors:  
vc14.jam: No such file or directory  
D:/boost_1_64_0/tools/build/src/build\toolset.jam:43: in toolset.using  
ERROR: rule "vc14.init" unknown in module "toolset".  
D:/boost_1_64_0/tools/build/src/build\project.jam:1052: in using  
project-config.jam:3: in modules.load  
D:/boost_1_64_0/tools/build/src\build-system.jam:249: in load-config  
D:/boost_1_64_0/tools/build/src\build-system.jam:412: in load-configuration-files  
D:/boost_1_64_0/tools/build/src\build-system.jam:524: in load  
D:\boost_1_64_0\tools\build\src/kernel\modules.jam:295: in import  
D:\boost_1_64_0\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
D:\boost_1_64_0\boost-build.jam:17: in module scope  
  
What is this rule vc14.init  and vc14.jam

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-03-21 22:44:02 UTC  
> Url: https://github.com/boostorg/build/issues/292#issuecomment-375120207  

AMDG  
  
On 03/21/2018 04:31 PM, rpitre wrote:  
> boostrap.bat works without problem: the engine and b2 compiles successfully using vc14.  
> But when running b2 I get those errors:  
> vc14.jam: No such file or directory  
> D:/boost_1_64_0/tools/build/src/build\toolset.jam:43: in toolset.using  
> ERROR: rule "vc14.init" unknown in module "toolset".  
> D:/boost_1_64_0/tools/build/src/build\project.jam:1052: in using  
> project-config.jam:3: in modules.load  
  
It looks like project-config.jam is generated  
incorrectly.  Delete it or replace `using vc14 ;`  
with `using msvc : 14.0 ;`  
  
> D:/boost_1_64_0/tools/build/src\build-system.jam:249: in load-config  
> D:/boost_1_64_0/tools/build/src\build-system.jam:412: in load-configuration-files  
> D:/boost_1_64_0/tools/build/src\build-system.jam:524: in load  
> D:\boost_1_64_0\tools\build\src/kernel\modules.jam:295: in import  
> D:\boost_1_64_0\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
> D:\boost_1_64_0\boost-build.jam:17: in module scope  
>   
> What is this rule vc14.init  and vc14.jam  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: rpitre  
> Created at: 2018-03-21 22:54:11 UTC  
> Url: https://github.com/boostorg/build/issues/292#issuecomment-375122325  

With you change the build does start but I get this error message:  
project-config.jam:3: syntax error at EOF  
warning: No toolsets are configured.  
warning: Configuring default toolset "msvc".  
warning: If the default is wrong, your build may not work correctly.  
  
I only have one line in the file:  
using msvc:14.0;

---

## Comment 3

> Username: eldiener  
> Created at: 2018-03-21 23:02:38 UTC  
> Url: https://github.com/boostorg/build/issues/292#issuecomment-375124102  

This should be:  
  
using msvc : 14.0 ;

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:42 UTC  
> Url: https://github.com/boostorg/build/issues/292#issuecomment-868936509  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
