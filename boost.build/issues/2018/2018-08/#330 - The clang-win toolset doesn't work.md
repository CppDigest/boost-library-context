# #330 - The clang-win toolset doesn't work [Closed]

> Username: pdimov  
> Created at: 2018-08-13 22:33:39 UTC  
> Updated at: 2018-10-29 23:35:43 UTC  
> Closed at: 2018-10-29 23:35:43 UTC  
> Url: https://github.com/boostorg/build/issues/330  

Trying to use the `clang-win` toolset fails with  
  
```  
C:/boost-git/develop/tools/build/src/tools\msvc.jam:1087: in set-setup-command  
*** argument error  
* rule virtual-target.from-file ( file : file-loc : project )  
* called with: (  : /C:/boost-git/develop/libs/assert : object(project-target)@129 )  
* missing argument file  
C:/boost-git/develop/tools/build/src/build\virtual-target.jam:970:see definition of rule 'virtual-target.from-file' being called  
C:/boost-git/develop/tools/build/src/tools\msvc.jam:652: in clang-win.compile.c++  
C:/boost-git/develop/tools/build/src/kernel\modules.jam:107: in modules.call-in  
C:/boost-git/develop/tools/build/src/util\indirect.jam:105: in indirect.call  
C:/boost-git/develop/tools/build/src/build\virtual-target.jam:902: in execute  
C:/boost-git/develop/tools/build/src/build\virtual-target.jam:821: in class@action.actualize  
C:/boost-git/develop/tools/build/src/build\virtual-target.jam:332: in actualize-action  
C:/boost-git/develop/tools/build/src/build\virtual-target.jam:518: in actualize-no-scanner  
C:/boost-git/develop/tools/build/src/build\virtual-target.jam:142: in class@virtual-target.actualize  
C:/boost-git/develop/tools/build/src/build\configure.jam:258: in try-find-build  
C:/boost-git/develop/tools/build/src/build\configure.jam:396: in find-builds-raw  
C:/boost-git/develop/tools/build/src/build\configure.jam:455: in configure.find-builds  
C:/boost-git/develop\boostcpp.jam:734: in boostcpp.deduce-address-model  
C:/boost-git/develop/tools/build/src/kernel\modules.jam:107: in modules.call-in  
C:/boost-git/develop/tools/build/src/util\indirect.jam:105: in indirect.call  
C:/boost-git/develop/tools/build/src/build\property.jam:144: in property.evaluate-conditionals-in-context  
C:/boost-git/develop/tools/build/src/build\targets.jam:1087: in evaluate-requirements  
C:/boost-git/develop/tools/build/src/build\targets.jam:1121: in common-properties2  
C:/boost-git/develop/tools/build/src/build\targets.jam:1017: in targets.common-properties  
C:/boost-git/develop/tools/build/src/build\targets.jam:1313: in class@basic-target.generate  
C:/boost-git/develop/tools/build/src/build\targets.jam:812: in generate-really  
C:/boost-git/develop/tools/build/src/build\targets.jam:784: in class@main-target.generate  
C:/boost-git/develop/tools/build/src/build\targets.jam:273: in class@project-target.generate  
C:/boost-git/develop/tools/build/src\build-system.jam:797: in load  
C:\boost-git\develop\tools\build\src/kernel\modules.jam:295: in import  
C:\boost-git\develop\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
C:\boost-git\develop\boost-build.jam:17: in module scope  
```  
  
I can see that this is related to the need to generate the MSVC setup script, and the missing `.SETUP-SCRIPT`, but I'm not sure what needs to be done to `clang-win.jam` to make it work.  
  
There's also the required `<compatibility>` option that `clang-cl` doesn't really need because it autodetects the installed Visual Studio toolset and, if not overridden, automatically matches the version of `cl.exe`. This on my installation for instance results in _MSC_VER being 1914, which `<compatibility>14.1`, were it supported, wouldn't have matched. But that's a secondary, and a minor, issue; main one is that it just doesn't work.

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-08-13 22:58:38 UTC  
> Url: https://github.com/boostorg/build/issues/330#issuecomment-412693538  

AMDG  
  
On 08/13/2018 04:33 PM, Peter Dimov wrote:  
> Trying to use the `clang-win` toolset fails with  
>   
> ```  
> C:/boost-git/develop/tools/build/src/tools\msvc.jam:1087: in set-setup-command  
> *** argument error  
> * rule virtual-target.from-file ( file : file-loc : project )  
> * called with: (  : /C:/boost-git/develop/libs/assert : object(project-target)@129 )  
> * missing argument file  
> <snip>  
> ```  
>   
> I can see that this is related to the need to generate the MSVC setup script, and the missing `.SETUP-SCRIPT`, but I'm not sure what needs to be done to `clang-win.jam` to make it work.  
>   
  
  It needs to use .SETUP-SCRIPT and .SETUP-OPTIONS instead  
of prepending the setup command to the compiler.  Actually,  
I think I'll fix msvc.jam to work if these variables are not  
defined.  It would be better, though, to factor out the  
code in msvc.jam for finding the setup script and let clang-win.jam  
invoke it, since it should be the same, right?  
  
> There's also the required `<compatibility>` option that `clang-cl` doesn't really need because it autodetects the installed Visual Studio toolset and, if not overridden, automatically matches the version of `cl.exe`. This on my installation for instance results in _MSC_VER being 1914, which `<compatibility>14.1`, were it supported, wouldn't have matched. But that's a secondary, and a minor, issue; main one is that it just doesn't work.  
>   
  
So, the right thing to do would be to choose the setup  
script to run based on the compatibility option,  
but not pass any -fmsc-version option explicitly?  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: pdimov  
> Created at: 2018-08-13 23:11:08 UTC  
> Url: https://github.com/boostorg/build/issues/330#issuecomment-412696430  

> So, the right thing to do would be to choose the setup script to run based on the compatibility option, but not pass any -fmsc-version option explicitly?  
  
Yes, I suppose. One interesting thing is that `clang-cl` actually works without any need for an environment to be set up; that is, `clang-cl` works (compiles and links by locating and invoking `link.exe`) even if invoked outside of a "developer command prompt". But I suppose we'll still need the environment for invoking f.ex. `lib.exe`.  
  
It would be nice if `clang-win` worked without `<compatibility>` having to be set; this would enable making `b2 toolset=clang-win` work without the need for a user-config, and potentially defaulting `toolset=clang` to that instead of `clang-linux` as is now the case.

---

## Comment 3

> Username: swatanabe  
> Created at: 2018-08-14 00:26:04 UTC  
> Url: https://github.com/boostorg/build/issues/330#issuecomment-412711327  

AMDG  
  
On 08/13/2018 05:11 PM, Peter Dimov wrote:  
> It would be nice if `clang-win` worked without `<compatibility>` having to be set;  
  
That's just a matter of finding the default version of  
msvc if none is specified.  msvc.jam already does this,  
so we only need to make it available to clang-win.  
  
In Christ,  
Steven Watanabe

---

## Comment 4

> Username: pdimov  
> Created at: 2018-10-29 23:35:43 UTC  
> Url: https://github.com/boostorg/build/issues/330#issuecomment-434119346  

Should be fixed by #361.
