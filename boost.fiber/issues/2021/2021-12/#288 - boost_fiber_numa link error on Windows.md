# #288 - boost_fiber_numa link error on Windows [Closed]

> Username: yurybura  
> Created at: 2021-12-24 17:37:18 UTC  
> Updated at: 2021-12-29 17:43:20 UTC  
> Closed at: 2021-12-29 17:43:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/288  

Reproducible with `b2 libs/fiber/build//boost_fiber_numa numa=on`  
  
Possible patch:  
```patch  
 build/Jamfile.v2 | 1 +  
 1 file changed, 1 insertion(+)  
  
diff --git a/build/Jamfile.v2 b/build/Jamfile.v2  
index 3efdb96..8a6b6dd 100644  
--- a/build/Jamfile.v2  
+++ b/build/Jamfile.v2  
@@ -130,6 +130,7 @@ lib boost_fiber_numa  
       numa/algo/work_stealing.cpp  
     :   
     <conditional>@numa  
+    <library>boost_fiber  
```  
  
Without this patch library boost_fiber_numa cannot link on Windows:  
```  
   Creating library D:\vcpkg\buildtrees\boost-fiber\x64-windows-dbg\boost\build\1cca5711dea309f44ef176a2abd1842a\boost_fiber_numa-vc142-mt-gd-x64-1_78.lib and object D:\vcpkg\buildtrees\boost-fiber\x64-windows-dbg\boost\build\1cca5711dea309f44ef176a2abd1842a\boost_fiber_numa-vc142-mt-gd-x64-1_78.exp  
work_stealing.obj : error LNK2019: unresolved external symbol "public: static class boost::fibers::context * __cdecl boost::fibers::context::active(void)" (?active@context@fibers@boost@@SAPEAV123@XZ) referenced in function "public: virtual class boost::fibers::context * __cdecl boost::fibers::numa::algo::work_stealing::pick_next(void)" (?pick_next@work_stealing@algo@numa@fibers@boost@@UEAAPEAVcontext@45@XZ)  
work_stealing.obj : error LNK2019: unresolved external symbol "public: virtual __cdecl boost::fibers::context::~context(void)" (??1context@fibers@boost@@UEAA@XZ) referenced in function "public: virtual void * __cdecl boost::fibers::context::`vector deleting destructor'(unsigned int)" (??_Econtext@fibers@boost@@UEAAPEAXI@Z)  
work_stealing.obj : error LNK2019: unresolved external symbol "public: void __cdecl boost::fibers::context::detach(void)" (?detach@context@fibers@boost@@QEAAXXZ) referenced in function "public: virtual void __cdecl boost::fibers::numa::algo::work_stealing::awakened(class boost::fibers::context *)" (?awakened@work_stealing@algo@numa@fibers@boost@@UEAAXPEAVcontext@45@@Z)  
work_stealing.obj : error LNK2019: unresolved external symbol "public: void __cdecl boost::fibers::context::attach(class boost::fibers::context *)" (?attach@context@fibers@boost@@QEAAXPEAV123@@Z) referenced in function "public: virtual class boost::fibers::context * __cdecl boost::fibers::numa::algo::work_stealing::pick_next(void)" (?pick_next@work_stealing@algo@numa@fibers@boost@@UEAAPEAVcontext@45@XZ)  
D:\vcpkg\buildtrees\boost-fiber\x64-windows-dbg\boost\build\1cca5711dea309f44ef176a2abd1842a\boost_fiber_numa-vc142-mt-gd-x64-1_78.dll : fatal error LNK1120: 4 unresolved externals  
...removing D:\vcpkg\buildtrees\boost-fiber\x64-windows-dbg\boost\build\1cca5711dea309f44ef176a2abd1842a\boost_fiber_numa-vc142-mt-gd-x64-1_78.lib  
...removing D:\vcpkg\buildtrees\boost-fiber\x64-windows-dbg\boost\build\1cca5711dea309f44ef176a2abd1842a\boost_fiber_numa-vc142-mt-gd-x64-1_78.pdb  
...skipped <pD:\vcpkg\buildtrees\boost-fiber\x64-windows-dbg\stage\lib>boost_fiber_numa-vc142-mt-gd-x64-1_78.dll for lack of <pD:\vcpkg\buildtrees\boost-fiber\x64-windows-dbg\boost\build\1cca5711dea309f44ef176a2abd1842a>boost_fiber_numa-vc142-mt-gd-x64-1_78.dll...  
...failed updating 1 target...  
ninja: build stopped: subcommand failed.  
```  
  
For more information please refer to: https://github.com/microsoft/vcpkg/pull/21928#discussion_r775048989 and https://github.com/microsoft/vcpkg/pull/21928#discussion_r775051357

---

## Comment 1

> Username: olk  
> Created at: 2021-12-29 17:43:20 UTC  
> Url: https://github.com/boostorg/fiber/issues/288#issuecomment-1002706465  

fixed, ty
