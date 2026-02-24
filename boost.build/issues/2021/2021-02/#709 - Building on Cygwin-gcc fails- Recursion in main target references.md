# #709 - Building on Cygwin/gcc fails: Recursion in main target references [Closed]

> Username: Lastique  
> Created at: 2021-02-05 21:05:01 UTC  
> Updated at: 2021-02-08 16:46:42 UTC  
> Closed at: 2021-02-08 16:46:42 UTC  
> Url: https://github.com/boostorg/build/issues/709  

Building Boost.Sync develop tests on Cygwin with gcc [fails](https://ci.appveyor.com/project/Lastique/sync/builds/37630462/job/lj5b149kjs3gluqg#L580):  
  
```  
b2 -j %NUMBER_OF_PROCESSORS% libs/sync/test variant=release toolset=%TOOLSET% address-model=%ADDRESS_MODEL% %CXXSTD% %B2_ARGS%  
C:/projects/boost-root/tools/build/src/build\targets.jam:609: in start-building from module targets  
error: Recursion in main target references  
error: the following target are being built currently:  
error: libs/sync/test/sync libs/config/checks/architecture/32 libs/config/checks/architecture/arm libs/config/checks/architecture/32  
C:/projects/boost-root/tools/build/src/build\targets.jam:776: in class@main-target.generate from module object(main-target)@4009  
C:/projects/boost-root/tools/build/src/build\targets.jam:934: in targets.generate-from-reference from module targets  
C:/projects/boost-root/tools/build/src/build\configure.jam:352: in find-builds-raw from module configure  
C:/projects/boost-root/tools/build/src/build\configure.jam:432: in configure.find-builds from module configure  
C:/projects/boost-root\boostcpp.jam:618: in boostcpp.deduce-address-model from module boostcpp  
C:/projects/boost-root/tools/build/src/kernel\modules.jam:107: in modules.call-in from module Jamfile<C:\projects\boost-root>  
C:/projects/boost-root/tools/build/src/util\indirect.jam:105: in indirect.call from module indirect  
C:/projects/boost-root/tools/build/src/build\property.jam:132: in property.evaluate-conditionals-in-context from module property  
C:/projects/boost-root/tools/build/src/build\targets.jam:1088: in evaluate-requirements from module targets  
C:/projects/boost-root/tools/build/src/build\targets.jam:1122: in common-properties2 from module targets  
C:/projects/boost-root/tools/build/src/build\targets.jam:1018: in targets.common-properties from module targets  
C:/projects/boost-root/tools/build/src/build\targets.jam:1314: in class@basic-target.generate from module object(typed-target)@1645  
C:/projects/boost-root/tools/build/src/build\targets.jam:812: in generate-really from module object(main-target)@4011  
C:/projects/boost-root/tools/build/src/build\targets.jam:784: in class@main-target.generate from module object(main-target)@4011  
C:/projects/boost-root/tools/build/src/build\targets.jam:934: in targets.generate-from-reference from module targets  
C:/projects/boost-root/tools/build/src/build\configure.jam:352: in find-builds-raw from module configure  
C:/projects/boost-root/tools/build/src/build\configure.jam:432: in configure.find-builds from module configure  
C:/projects/boost-root\boostcpp.jam:657: in boostcpp.deduce-architecture from module boostcpp  
C:/projects/boost-root/tools/build/src/kernel\modules.jam:107: in modules.call-in from module Jamfile<C:\projects\boost-root>  
C:/projects/boost-root/tools/build/src/util\indirect.jam:105: in indirect.call from module indirect  
C:/projects/boost-root/tools/build/src/build\property.jam:132: in property.evaluate-conditionals-in-context from module property  
C:/projects/boost-root/tools/build/src/build\targets.jam:1088: in evaluate-requirements from module targets  
C:/projects/boost-root/tools/build/src/build\targets.jam:1122: in common-properties2 from module targets  
C:/projects/boost-root/tools/build/src/build\targets.jam:1018: in targets.common-properties from module targets  
C:/projects/boost-root/tools/build/src/build\targets.jam:1314: in class@basic-target.generate from module object(typed-target)@1643  
C:/projects/boost-root/tools/build/src/build\targets.jam:812: in generate-really from module object(main-target)@4009  
C:/projects/boost-root/tools/build/src/build\targets.jam:784: in class@main-target.generate from module object(main-target)@4009  
C:/projects/boost-root/tools/build/src/build\targets.jam:934: in targets.generate-from-reference from module targets  
C:/projects/boost-root/tools/build/src/build\configure.jam:352: in find-builds-raw from module configure  
C:/projects/boost-root/tools/build/src/build\configure.jam:432: in configure.find-builds from module configure  
C:/projects/boost-root\boostcpp.jam:618: in boostcpp.deduce-address-model from module boostcpp  
C:/projects/boost-root/tools/build/src/kernel\modules.jam:107: in modules.call-in from module Jamfile<C:\projects\boost-root>  
C:/projects/boost-root/tools/build/src/util\indirect.jam:105: in indirect.call from module indirect  
C:/projects/boost-root/tools/build/src/build\property.jam:132: in property.evaluate-conditionals-in-context from module property  
C:/projects/boost-root/tools/build/src/build\targets.jam:1088: in evaluate-requirements from module targets  
C:/projects/boost-root/tools/build/src/build\targets.jam:1122: in common-properties2 from module targets  
C:/projects/boost-root/tools/build/src/build\targets.jam:1018: in targets.common-properties from module targets  
C:/projects/boost-root/tools/build/src/build\targets.jam:1314: in class@basic-target.generate from module object(alias-target-class)@3987  
C:/projects/boost-root/tools/build/src/build\targets.jam:812: in generate-really from module object(main-target)@4002  
C:/projects/boost-root/tools/build/src/build\targets.jam:784: in class@main-target.generate from module object(main-target)@4002  
C:/projects/boost-root/tools/build/src/build\targets.jam:273: in class@project-target.generate from module object(project-target)@3931  
C:/projects/boost-root/tools/build/src\build-system.jam:797: in load from module build-system  
C:/projects/boost-root/tools/build/src/kernel\modules.jam:295: in import from module modules  
C:/projects/boost-root/tools/build/src/kernel/bootstrap.jam:139: in boost-build from module  
C:/projects/boost-root/boost-build.jam:17: in module scope from module  
```  
  
This happens with ADDRESS_MODEL=64 and 32. The same commit on master builds successfully.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2021-02-05 22:05:14 UTC  
> Url: https://github.com/boostorg/build/issues/709#issuecomment-774313526  

Thanks for the report... I just got a local cygwin setup working, took a couple days, so I can decipher this.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2021-02-07 20:00:45 UTC  
> Url: https://github.com/boostorg/build/issues/709#issuecomment-774749497  

The problem stems from the failed attempt by Boost.Config to avoid adding the boostcpp module conditionals here https://github.com/boostorg/config/blob/develop/checks/architecture/Jamfile.jam#L11 -- Those property removals have, essentially, no effect as they don't specify the full actual name of the indirect rules. I'm trying to find a reasonable work around though.
