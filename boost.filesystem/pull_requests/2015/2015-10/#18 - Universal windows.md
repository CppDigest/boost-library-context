# #18 Universal windows [Closed]

> Username: mauve  
> Created at: 2015-10-01 13:44:09 UTC  
> Updated at: 2020-05-01 21:00:45 UTC  
> Closed at: 2020-05-01 21:00:44 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18  

Add support for Universal Windows Platform

---

## Comment 1

> Username: StSimmons  
> Created_at: 2015-10-31 17:49:55 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-152755583  

I've tried merging this in and testing - but a number of APIs don't seem to be available in Win10 on a boost cli build. Building with: toolset=msvc-14.0 link=static windows-api=store  
  
Notably: GetFullPathName, GetCurrentDirectory etc.

---

## Comment 2

> Username: mauve  
> Created_at: 2015-11-01 09:49:48 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-152812900  

@SimmonsTech I just rebased on develop and repushed, I also compiled it again locally and it compiles cleanly for me. You however need to have the Windows 10 SDK installed, according to `C:\Program Files (x86)\Windows Kits\10\Include\10.0.10240.0\um\fileapi.h` GetFullPathName is available, that is however not the case with the windows 8.1 SDK (where it is only available in the desktop api partition).

---

## Comment 3

> Username: StSimmons  
> Created_at: 2015-11-01 10:03:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-152814108  

Interesting, I do indeed have the Win10 SDK installed but still see these issues. I'll have another dig. You are using the same CLI arguments?

---

## Comment 4

> Username: mauve  
> Created_at: 2015-11-01 10:21:19 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-152814617  

```  
~\DEV\other-repositories\boost\libs\filesystem [universal-windows] > git lg -1  
* d8b015a - (HEAD -> universal-windows) universal-windows: Add missing boost.predef includes (43 minutes ago) <Mikael Olenfalk>  
~\DEV\other-repositories\boost\libs\filesystem [universal-windows] > cd .\build\  
~\DEV\other-repositories\boost\libs\filesystem\build [universal-windows] > rm -recurse -force ..\..\..\bin.v2\  
~\DEV\other-repositories\boost\libs\filesystem\build [universal-windows] > ..\..\..\b2.exe toolset=msvc-14.0 link=static windows-api=store  
Performing configuration checks  
  
    - 32-bit                   : yes  
    - arm                      : no  
    - mips1                    : no  
    - power                    : no  
    - sparc                    : no  
    - x86                      : yes  
    - symlinks supported       : no  
    - junctions supported      : yes  
    - hardlinks supported      : yes  
...patience...  
...found 1266 targets...  
...updating 17 targets...  
common.mkdir ..\..\..\bin.v2\libs  
common.mkdir ..\..\..\bin.v2\libs\filesystem  
common.mkdir ..\..\..\bin.v2\libs\filesystem\build  
common.mkdir ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0  
common.mkdir ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug  
common.mkdir ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\link-static  
common.mkdir ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\link-static\threading-multi  
common.mkdir ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\link-static\threading-multi\windows-api-store  
compile-c-c++ ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\link-static\threading-multi\windows-api-store\codecvt_error_category.obj  
codecvt_error_category.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\link-static\threading-multi\windows-api-store\operations.obj  
operations.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\link-static\threading-multi\windows-api-store\path.obj  
path.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\link-static\threading-multi\windows-api-store\path_traits.obj  
path_traits.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\link-static\threading-multi\windows-api-store\portability.obj  
portability.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\link-static\threading-multi\windows-api-store\unique_path.obj  
unique_path.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\link-static\threading-multi\windows-api-store\utf8_codecvt_facet.obj  
utf8_codecvt_facet.cpp  
compile-c-c++ ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\link-static\threading-multi\windows-api-store\windows_file_codecvt.obj  
windows_file_codecvt.cpp  
msvc.archive ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\link-static\threading-multi\windows-api-store\libboost_filesystem-vc140-mt-gd-1_59.lib  
...updated 17 targets...  
~\DEV\other-repositories\boost\libs\filesystem\build [universal-windows] >  
```

---

## Comment 5

> Username: Orphis  
> Created_at: 2016-04-22 12:09:31 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-213397802  

@mauve @StSimmons Any update on this PR? Boost 1.61.0 b1 still doesn't build for WindowsStore.

---

## Comment 6

> Username: mauve  
> Created_at: 2016-04-22 12:50:12 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-213414760  

@Orphis what are you trying to build? I haven't tried in a while but can give it a go this weekend.

---

## Comment 7

> Username: Orphis  
> Created_at: 2016-04-25 09:05:57 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-214219028  

Nothing, you know. Just the Boost filesystem lib for WindowsStore platforms!

---

## Comment 8

> Username: fxb  
> Created_at: 2016-04-27 14:28:33 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-215101470  

@Beman: It would be nice to also get this pull request merged to make Boost.Filesystem compile for the Universal Windows Platform.  
  
I rebased @mauve's changes on latest develop and they compile and work fine.  
  
If you have any comments or suggestions on these changes, I'll be happy to look at those.

---

## Comment 9

> Username: zdunker  
> Created_at: 2018-05-10 23:15:41 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-388213414  

Really need UWP filesystem lib!

---

## Comment 10

> Username: crule42  
> Created_at: 2019-01-10 23:56:32 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-453308210  

I needed this patch also, so I made the changes on top of the latest release build 1.69.0 as of this writing (not master or develop). Here is the patch in case others want it also.  
  
  
[0001-Implemented-changes-from-PR-18-on-top-of-the-latest-.zip](https://github.com/boostorg/filesystem/files/2747512/0001-Implemented-changes-from-PR-18-on-top-of-the-latest-.zip)

---

## Comment 11

> Username: Lastique  
> Created_at: 2019-04-16 09:19:43 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-483580684  

If this is still relevant, please rebase against the current develop.

---

## Comment 12

> Username: soroshsabz  
> Created_at: 2020-04-11 20:18:54 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-612505705  

@mauve Did you want to update this PR?

---

## Comment 13

> Username: Lastique  
> Created_at: 2020-05-01 21:00:44 UTC  
> Url: https://github.com/boostorg/filesystem/pull/18#issuecomment-622566496  

Closing as the PR is inactive.

---
