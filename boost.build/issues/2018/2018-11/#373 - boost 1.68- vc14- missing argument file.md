# #373 - boost 1.68/ vc14: missing argument file [Open]

> Username: tmykha  
> Created at: 2018-11-23 10:31:03 UTC  
> Updated at: 2021-06-11 01:55:59 UTC  
> Url: https://github.com/boostorg/build/issues/373  

I was trying to build boost_1_68_0 with msvc-14.0 toolset and faced with an error. Please see the output:  
  
```  
D:\Private\dev\boost\boost_1_68_0>b2 install -j2 toolset=msvc-14.0 address-model=32 architecture=x86 link=static threading=multi --build-type=complete stage  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/tools\msvc.jam:1087: in set-setup-command  
*** argument error  
* rule virtual-target.from-file ( file : file-loc : project )  
* called with: (  : /D:/Private/dev/boost/boost_1_68_0 : object(project-target)@131 )  
* missing argument file  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\virtual-target.jam:970:see definition of rule 'virtual-target.from-file' being called  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/tools\msvc.jam:652: in msvc.compile.c++  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/kernel\modules.jam:107: in modules.call-in  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/util\indirect.jam:105: in indirect.call  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\virtual-target.jam:902: in execute  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\virtual-target.jam:821: in class@action.actualize  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\virtual-target.jam:332: in actualize-action  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\virtual-target.jam:518: in actualize-no-scanner  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\virtual-target.jam:142: in class@virtual-target.actualize  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\configure.jam:258: in try-find-build  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\configure.jam:396: in find-builds-raw  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\configure.jam:455: in configure.find-builds  
D:/Private/dev/boost/boost_1_68_0\boostcpp.jam:690: in boostcpp.deduce-address-model  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/kernel\modules.jam:107: in modules.call-in  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/util\indirect.jam:105: in indirect.call  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\property.jam:144: in property.evaluate-conditionals-in-context  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\targets.jam:1087: in evaluate-requirements  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\targets.jam:1121: in common-properties2  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\targets.jam:1017: in targets.common-properties  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\targets.jam:1313: in alias-target-class.generate  
D:/Private/dev/boost/boost_1_68_0\boostcpp.jam:500: in build-multiple  
D:/Private/dev/boost/boost_1_68_0\boostcpp.jam:490: in class@top-level-target.generate  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\targets.jam:812: in generate-really  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\targets.jam:784: in class@main-target.generate  
D:/Private/dev/boost/boost_1_68_0/tools/build/src\build-system.jam:797: in load  
D:\Private\dev\boost\boost_1_68_0\tools\build\src/kernel\modules.jam:295: in import  
D:\Private\dev\boost\boost_1_68_0\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
D:\Private\dev\boost\boost_1_68_0\boost-build.jam:17: in module scope  
```  
  
Also attaching my project-config.jam file:  
  
```  
import option ;   
using msvc : 14.0 : "C:\Program Files (x86)\Microsoft Visual Studio\2017\Community\VC\Tools\MSVC\14.15.26726\bin\Hostx64\x64\cl.exe" ;  
option.set keep-going : false ;   
```  
  
Please correct me if I'm doing something wrong. Thanks.

---

## Comment 1

> Username: eldiener  
> Created at: 2018-11-23 12:08:15 UTC  
> Url: https://github.com/boostorg/build/issues/373#issuecomment-441225343  

Your project-config.jam using msvc path is for msvc-14.1 and not msvc-14.0.

---

## Comment 2

> Username: tmykha  
> Created at: 2018-11-23 12:14:43 UTC  
> Url: https://github.com/boostorg/build/issues/373#issuecomment-441226556  

@eldiener Thanks. I corrected it in project-config.jam, ran `b2 install -j2 toolset=msvc-14.1 address-model=32 architecture=x86 link=static threading=multi --build-type=complete --stagedir=D:\Private\dev\boost\vs2017\Win\x86` and got the same error:  
  
```  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/tools\msvc.jam:1087: in set-setup-command  
*** argument error  
* rule virtual-target.from-file ( file : file-loc : project )  
* called with: (  : /D:/Private/dev/boost/boost_1_68_0 : object(project-target)@131 )  
* missing argument file  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\virtual-target.jam:970:see definition of rule 'virtual-target.from-file' being called  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/tools\msvc.jam:652: in msvc.compile.c++  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/kernel\modules.jam:107: in modules.call-in  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/util\indirect.jam:105: in indirect.call  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\virtual-target.jam:902: in execute  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\virtual-target.jam:821: in class@action.actualize  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\virtual-target.jam:332: in actualize-action  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\virtual-target.jam:518: in actualize-no-scanner  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\virtual-target.jam:142: in class@virtual-target.actualize  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\configure.jam:258: in try-find-build  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\configure.jam:396: in find-builds-raw  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\configure.jam:455: in configure.find-builds  
D:/Private/dev/boost/boost_1_68_0\boostcpp.jam:690: in boostcpp.deduce-address-model  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/kernel\modules.jam:107: in modules.call-in  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/util\indirect.jam:105: in indirect.call  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\property.jam:144: in property.evaluate-conditionals-in-context  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\targets.jam:1087: in evaluate-requirements  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\targets.jam:1121: in common-properties2  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\targets.jam:1017: in targets.common-properties  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\targets.jam:1313: in alias-target-class.generate  
D:/Private/dev/boost/boost_1_68_0\boostcpp.jam:500: in build-multiple  
D:/Private/dev/boost/boost_1_68_0\boostcpp.jam:490: in class@top-level-target.generate  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\targets.jam:812: in generate-really  
D:/Private/dev/boost/boost_1_68_0/tools/build/src/build\targets.jam:784: in class@main-target.generate  
D:/Private/dev/boost/boost_1_68_0/tools/build/src\build-system.jam:797: in load  
D:\Private\dev\boost\boost_1_68_0\tools\build\src/kernel\modules.jam:295: in import  
D:\Private\dev\boost\boost_1_68_0\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
D:\Private\dev\boost\boost_1_68_0\boost-build.jam:17: in module scope  
```

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-06-11 01:55:21 UTC  
> Url: https://github.com/boostorg/build/issues/373#issuecomment-859203486  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
