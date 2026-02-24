# #455 - b2 tries to open .rsp files [Open]

> Username: Timmmm  
> Created at: 2019-06-28 16:24:32 UTC  
> Updated at: 2021-06-11 01:55:31 UTC  
> Url: https://github.com/boostorg/build/issues/455  

If you have a custom `user-config.jam` containing e.g.   
  
```  
using msvc : : "C:/Program Files (x86)/Microsoft Visual Studio/2017/Professional/VC/Tools/MSVC/14.16.27023/bin/Hostx64/x64/cl.exe" ;  
```  
  
And you try to build Boost with  
  
```  
bootstrap  
b2 --user-config=C:/path/to/user-config.jam  
```  
  
Then it works fine with Boost 1.69.0, but with 1.70.0 you get a load of dialog boxes saying windows doesn't know how to open .rsp files.

---

## Comment 1

> Username: Timmmm  
> Created at: 2019-07-01 11:23:55 UTC  
> Url: https://github.com/boostorg/build/issues/455#issuecomment-507224591  

I have bisected this. The problem is introduced into the main repo by [this commit](https://github.com/boostorg/boost/commit/0abf39a2b21e96d67635c666d2e5834edaa3a071). I bisected the `tools/build` subrepo too but had to skip a few commits because of some unrelated error. This is the range of interest (from newest to oldest):  
  
* [Clean up msvc initialization.](https://github.com/boostorg/build/commit/9540e69b3ff89da704731c31472fa91ffda8f3ad) - Bad  
* [Clear the error instead of not setting it.](https://github.com/boostorg/build/commit/de026bc61eefe4e63b78c50c4b04567ea155065d) - Skipped  
* [If the vswhere lookup fails, keep searching for others](https://github.com/boostorg/build/commit/1691e6194c470e237ddf595bdbd9fb4e0a3320c3) - Skipped  
* [Don't accidentally detect msvc-14.1 as msvc-14.2.](https://github.com/boostorg/build/commit/4ad28074f43fbde705aee2c71ec64acc56ceea18) - Skipped  
* [Added support for actually building with msvc-14.2](https://github.com/boostorg/build/commit/0a93cc2b97cafc9fbbd6c1dbd0c94bb9c6bd65ee) - Skipped  
* [Future one is "UNK"](https://github.com/boostorg/build/commit/bee2ef619bc3397852f33286515f3174751989a1) - Good  
  
The error for the skipped ones is:  
  
```  
C:/Users/me/boost/tools/build/src/tools\msvc.jam:1388: in configure-really  
*** argument error  
* rule generate-setup-cmd ( version : command : parent : options * : cpu : global-setup ? : default-global-setup-options : default-setup )  
* called with: ( 14.16.27023 14.16.27023 : C:/Program Files (x86)/Microsoft Visual Studio/2017/Professional/VC/Tools/MSVC/14.16.27023/bin/Hostx64/x64 : C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.16.27023\bin\Hostx64 : <command>C:\Program Files (x86)\Microsoft Visual Studio\2017\Professional\VC\Tools\MSVC\14.16.27023\bin\Hostx64\x64\cl.exe <command>C:/Program Files (x86)/Microsoft Visual Studio/2017/Professional/VC/Tools/MSVC/14.16.27023/bin/Hostx64/x64/cl.exe : i386 :  : x86 : vcvars32.bat )  
* extra argument 14.16.27023  
C:/Users/me/boost/tools/build/src/tools\msvc.jam:1003:see definition of rule 'generate-setup-cmd' being called  
C:/Users/me/boost/tools/build/src/tools\msvc.jam:393: in configure  
C:/Users/me/boost/tools/build/src/tools\msvc.jam:345: in msvc.init  
C:/Users/me/boost/tools/build/src/build\toolset.jam:44: in toolset.using  
C:/Users/me/boost/tools/build/src/build\project.jam:1104: in using  
C:\Users\me\user-config.jam:1: in modules.load  
C:/Users/me/boost/tools/build/src\build-system.jam:255: in load-config  
C:/Users/me/boost/tools/build/src\build-system.jam:449: in load-configuration-files  
C:/Users/me/boost/tools/build/src\build-system.jam:607: in load  
C:\Users\me\boost\tools\build\src/kernel\modules.jam:295: in import  
C:\Users\me\boost\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
C:\Users\me\boost\boost-build.jam:17: in module scope  
```

---

## Comment 2

> Username: Timmmm  
> Created at: 2019-07-01 11:51:19 UTC  
> Url: https://github.com/boostorg/build/issues/455#issuecomment-507232493  

Ok I'm pretty sure the problem is in [Clean up msvc initialization.](https://github.com/boostorg/build/commit/9540e69b3ff89da704731c31472fa91ffda8f3ad). I cherry-picked it onto [Future one is "UNK"](https://github.com/boostorg/build/commit/bee2ef619bc3397852f33286515f3174751989a1) and still get the failure.  
  
I don't know enough about b2 to go any further I'm afraid.

---

## Comment 3

> Username: sitsofe  
> Created at: 2019-07-02 12:38:44 UTC  
> Url: https://github.com/boostorg/build/issues/455#issuecomment-507658460  

Are there any clues on this one @swatanabe?

---

## Comment 4

> Username: eldiener  
> Created at: 2019-07-27 22:57:55 UTC  
> Url: https://github.com/boostorg/build/issues/455#issuecomment-515719161  

I have also seen this occur occassionally in the 'develop' branch of Build when I try to invoke an invalid toolset, for which I have no 'using...' definition, or a toolset whose command as specified is invalid in some way.

---

## Comment 5

> Username: stale[bot]  
> Created at: 2021-06-11 01:54:55 UTC  
> Url: https://github.com/boostorg/build/issues/455#issuecomment-859203282  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
