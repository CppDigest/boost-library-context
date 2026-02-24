# #64 - Regression when building using msvc-12.0_xp toolset [Closed]

> Username: dbarkar  
> Created at: 2015-03-12 20:47:50 UTC  
> Updated at: 2015-06-03 19:21:02 UTC  
> Closed at: 2015-06-03 19:21:02 UTC  
> Url: https://github.com/boostorg/build/issues/64  

When I try to build boost with toolset=msvc-12.0_xp I get the error:  
  
```  
D:/boost/src/tools/build/src/tools\msvc.jam:288: in configure-version-specific  
*** argument error  
* rule path.make ( native )  
* called with: (  )  
* missing argument native  
D:/boost/src/tools/build/src/util\path.jam:479:see definition of rule 'path.make' being called  
D:/boost/src/tools/build/src/tools\msvc.jam:1141: in configure-really  
D:/boost/src/tools/build/src/tools\msvc.jam:200: in configure  
D:/boost/src/tools/build/src/tools\msvc.jam:152: in msvc.init  
D:/boost/src/tools/build/src/build\toolset.jam:43: in toolset.using  
D:/boost/src/tools/build/src\build-system.jam:461: in process-explicit-toolset-requests  
D:/boost/src/tools/build/src\build-system.jam:527: in load  
D:\boost\src\tools\build\src/kernel\modules.jam:289: in import  
D:\boost\src\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
D:\boost\src\boost-build.jam:17: in module scope`  
```  
  
And everything is fine if I build boost-1.56 in the same environment. Also replacing msvc.jam helps.

---

## Comment 1

> Username: vprus  
> Created at: 2015-03-12 21:03:15 UTC  
> Url: https://github.com/boostorg/build/issues/64#issuecomment-78616171  

What is msvc-12.0_xp? I'm afraid you'd need to add ECHO prints along the stacktrace, since otherwise it's hard to understand what is going on your system.

---

## Comment 2

> Username: dbarkar  
> Created at: 2015-03-12 21:44:02 UTC  
> Url: https://github.com/boostorg/build/issues/64#issuecomment-78634628  

It's Visual Studio 2013 toolset for Windows XP targets (default toolset is targeting Win7 and newer).   
Ok, I found the problem block at line 285:  
  
```  
# Starting with Visual Studio 2013 the CRT is split into a desktop and app dll.  
#If targeting WinRT and 12.0 set lib path to link against app CRT.  
if [ MATCH "(12)" : $(version) ]  
{  
    ECHO $(version) ;  
    ECHO [ default-path $(version) ] ;  
    local VCPath = [ path.parent [ path.make [ default-path $(version) ] ] ] ;  
    local storeLibPath = [ path.join [ path.join $(VCPath) "lib" ] "store" ] ;  
    toolset.flags $(toolset).link LINKPATH $(conditions)/<windows-api>store/$(.cpu-arch-i386) : [ path.native $(storeLibPath) ] ;  
    toolset.flags $(toolset).link LINKPATH $(conditions)/<windows-api>store/$(.cpu-arch-amd64) : [ path.native [ path.join $(storeLibPath) "amd64" ] ] ;  
    toolset.flags $(toolset).link LINKPATH $(conditions)/<windows-api>store/$(.cpu-arch-arm) : [ path.native [ path.join $(storeLibPath) "arm" ] ] ;  
}  
```  
  
The function is called twice (no idea why), with version == 12.0 and with version = 12.0_xp. [ default-path $(version) ] when version == 12.0_xp returns null and here is the error.  
If I not mistaken this block is needed only when targeting WinRT, it shall not be called for WinXP targets anyway.  
Removing this block removes the error.

---

## Comment 3

> Username: vprus  
> Created at: 2015-06-03 19:21:02 UTC  
> Url: https://github.com/boostorg/build/issues/64#issuecomment-108586515  

Fixed in a0cf4ad.
