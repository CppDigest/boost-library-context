# #463 - Unusual "bug" in Boost Build with gcc usage requirements [Open]

> Username: eldiener  
> Created at: 2019-08-01 17:12:35 UTC  
> Updated at: 2021-06-11 01:55:30 UTC  
> Url: https://github.com/boostorg/build/issues/463  

Given a user-config.jam of:  
  
```  
import toolset ;  
import feature ;  
using gcc : 8.3 : "C:/Utilities/mingw-w64/i686-8.3.0/bin/g++" ;  
using gcc : 8.1 : "C:/Utilities/mingw-w64/i686-8.1.0-posix-dwarf-rt_v6-rev0/mingw32/bin/g++" ;  
# using gcc : 8.3 : "C:/Utilities/mingw-w64/i686-8.3.0/bin/g++" : : <address-model>32 ;  
# using gcc : 8.1 : "C:/Utilities/mingw-w64/i686-8.1.0-posix-dwarf-rt_v6-rev0/mingw32/bin/g++" : : <address-model>32 ;  
feature.subfeature toolset gcc : tsestd : c03 c11 c14 c17 : optional composite propagated ;  
feature.compose <toolset-gcc:tsestd>c03 : <cxxflags>-std=c++03 ;  
feature.compose <toolset-gcc:tsestd>c11 : <cxxflags>-std=c++11 ;  
feature.compose <toolset-gcc:tsestd>c14 : <cxxflags>-std=c++14 ;  
feature.compose <toolset-gcc:tsestd>c17 : <cxxflags>-std=c++1z ;  
```  
  
running:  
  
`b2 toolset=gcc-8.3`  
  
or  
  
`b2 toolset=gcc-8.3 address-model=32`  
  
or  
  
`b2 toolset=gcc-8.3 address-model=64`  
  
in any library's test directory will run "successfully", ie. no Boost Build error messages.  
  
Now comment out the first two 'using gcc' lines and remove the comment from the next two 'using gcc' lines, so that the usage requirement for \<address-model\>32 is now a part of the toolset definitions. Running the same b2 commands from any library's test directory will produce:  
  
```  
E:/Programming/VersionControl/modular-boost/tools/build/src/build\feature.jam:787: in expand-composites from module feature  
error: explicitly-specified values of non-free feature <toolset-gcc:version> conflict  
error: existing values: 8.1 8.3  
error: value from expanding  <toolset-gcc:version>8.3 : 8.3  
E:/Programming/VersionControl/modular-boost/tools/build/src/build\feature.jam:872: in feature.expand from module feature  
E:/Programming/VersionControl/modular-boost/tools/build/src/build\targets.jam:1103: in evaluate-requirements from module targets  
E:/Programming/VersionControl/modular-boost/tools/build/src/build\targets.jam:1121: in common-properties2 from module targets  
E:/Programming/VersionControl/modular-boost/tools/build/src/build\targets.jam:1017: in targets.common-properties from module targets  
E:/Programming/VersionControl/modular-boost/tools/build/src/build\targets.jam:1313: in class@basic-target.generate from module object(alias-target-class)@6339  
E:/Programming/VersionControl/modular-boost/tools/build/src/build\targets.jam:812: in generate-really from module object(main-target)@6615  
E:/Programming/VersionControl/modular-boost/tools/build/src/build\targets.jam:784: in class@main-target.generate from module object(main-target)@6615  
E:/Programming/VersionControl/modular-boost/tools/build/src/build\targets.jam:273: in class@project-target.generate from module object(project-target)@6233  
E:/Programming/VersionControl/modular-boost/tools/build/src\build-system.jam:797: in load from module build-system  
E:\Programming\VersionControl\modular-boost\tools\build\src/kernel\modules.jam:295: in import from module modules  
E:\Programming\VersionControl\modular-boost\tools\build\src/kernel/bootstrap.jam:139: in boost-build from module  
E:\Programming\VersionControl\modular-boost\boost-build.jam:17: in module scope from module  
```  
  
This is with the latest Boost Build 'develop' branch. It seems as if the gcc usage requirements do not work with feature composition. I do not know if this is a gcc.jam problem or a more general Boost Build problem with usage requirements and feature composition. But it is a definite issue with the latest 'develop' branch of Boost Build. I see no reason why this should not work properly so i think this is a  bug and should be fixed in Boost Build. In practical usage it keeps me from specifying different address models for gcc toolset definitions as well as gcc feature compositions in my user-config.jam file.

---

## Comment 1

> Username: stale[bot]  
> Created at: 2021-06-11 01:54:54 UTC  
> Url: https://github.com/boostorg/build/issues/463#issuecomment-859203275  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
