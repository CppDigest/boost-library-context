# #301 - Boost 1.67.0: MinGW GCC 7.2.0 b2 error [Open]

> Username: ruslo  
> Created at: 2018-04-16 10:43:26 UTC  
> Updated at: 2021-06-26 03:10:12 UTC  
> Url: https://github.com/boostorg/build/issues/301  

`b2` error while trying to configure Boost 1.67.0 with MinGW.  
  
Steps to reproduce:  
```  
[boost_1_67_0]> bootstrap.bat gcc  
```  
  
```  
[boost_1_67_0]> b2 target-os=windows -a link=static threading=multi variant=debug,release --layout=tagged toolset=gcc --user-config=C:/.../boost_1_67_0/boost.user.jam --with-system  
```  
  
Where `boost.user.jam` content:  
  
```  
using gcc  
  :  
  : "C:/.../mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/mingw64/bin/g++.exe" -std=c++17  
  : <archiver>"C:/.../mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/mingw64/bin/ar.exe"  
 <ranlib>"C:/.../mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/mingw64/bin/ranlib.exe"  
;  
```  
  
Error message:  
```  
'C:/.../mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/mingw64/bin/g++.exe" "-std' is not recognized as an internal or external command, operable program or batch file.  
'C:/.../mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/mingw64/bin/g++.exe" "-std' is not recognized as an internal or external command, operable program or batch file.  
'C:/.../mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/mingw64/bin/g++.exe" "-std' is not recognized as an internal or external command, operable program or batch file.  
'C:/.../mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/mingw64/bin/g++.exe" "-std' is not recognized as an internal or external command, operable program or batch file.  
C:/.../boost_1_67_0/tools/build/src/tools\gcc.jam:279: in gcc.init  
*** argument error  
* rule regex.split ( string separator )  
* called with: ( [.] )  
* missing argument separator  
(builtin):see definition of rule 'regex.split' being called  
C:/.../boost_1_67_0/tools/build/src/build\toolset.jam:44: in toolset.using  
C:/.../boost_1_67_0/tools/build/src/build\project.jam:1052: in using  
C:\...\boost_1_67_0\boost.user.jam:6: in modules.load  
C:/.../boost_1_67_0/tools/build/src\build-system.jam:255: in load-config  
C:/.../boost_1_67_0/tools/build/src\build-system.jam:449: in load-configuration-files  
C:/.../boost_1_67_0/tools/build/src\build-system.jam:607: in load  
C:\...\boost_1_67_0\tools\build\src/kernel\modules.jam:295: in import  
C:\...\boost_1_67_0\tools\build\src/kernel/bootstrap.jam:139: in boost-build  
C:\...\boost_1_67_0\boost-build.jam:17: in module scope  
```  
  
Regression note: Same configuration worked fine with Boost 1.64.0

---

## Comment 1

> Username: swatanabe  
> Created at: 2018-05-13 02:40:26 UTC  
> Url: https://github.com/boostorg/build/issues/301#issuecomment-388596863  

AMDG  
  
On 04/16/2018 04:43 AM, Ruslan Baratov wrote:  
> `b2` error while trying to configure Boost 1.67.0 with MinGW.  
>   
> Steps to reproduce:  
> ```  
> [boost_1_67_0]> bootstrap.bat gcc  
> ```  
>   
> ```  
> [boost_1_67_0]> b2 target-os=windows -a link=static threading=multi variant=debug,release --layout=tagged toolset=gcc --user-config=C:/.../boost_1_67_0/boost.user.jam --with-system  
> ```  
>   
> Where `boost.user.jam` content:  
>   
> ```  
> using gcc  
>   :  
>   : "C:/.../mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/mingw64/bin/g++.exe" -std=c++17  
>   : <archiver>"C:/.../mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/mingw64/bin/ar.exe"  
>  <ranlib>"C:/.../mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/mingw64/bin/ranlib.exe"  
> ;  
  
The simplest workaround is to pass cxxstd=17 to b2 instead of  
hard-coding it in the toolset initialization.  
  
> ```  
>   
> Error message:  
> ```  
> 'C:/.../mingw-w64/x86_64-7.2.0-posix-seh-rt_v5-rev1/mingw64/bin/g++.exe" "-std' is not recognized as an internal or external command, operable program or batch file.  
  
It looks like the quotes on the command passed to SHELL are  
broken somehow.  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: ruslo  
> Created at: 2018-05-14 13:55:53 UTC  
> Url: https://github.com/boostorg/build/issues/301#issuecomment-388825403  

> The simplest workaround is to pass cxxstd=17 to b2 instead of hard-coding it in the toolset initialization.  
  
I think ` "-std' is not recognized as an internal or external command` is just a warning or unrelated message. E.g. I have tried to add `-fvisibility=hidden` in the same way and got this message too, however Boost builds fine.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-06-26 03:09:29 UTC  
> Url: https://github.com/boostorg/build/issues/301#issuecomment-868936463  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2  
This issue has been automatically marked as "transition" to indicate the potential for needing transition to the new B2 development project.
