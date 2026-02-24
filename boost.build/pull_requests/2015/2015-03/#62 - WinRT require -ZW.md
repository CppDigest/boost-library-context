# #62 WinRT require /ZW [Closed]

> Username: john-peterson  
> Created at: 2015-03-09 20:36:26 UTC  
> Updated at: 2021-10-02 22:20:16 UTC  
> Closed at: 2015-09-10 11:08:48 UTC  
> Url: https://github.com/boostorg/build/pull/62  

there's no /ZW in https://github.com/boostorg/build/blob/develop/src/tools/msvc.jam so both link=shared and link=static are without the Windows namespace and without __cplusplus_winrt. this cause errors like this with both link=shared and link=static  
  
```  
git clone --depth=1 --no-single-branch http://github.com/boostorg/boost.git .  
git checkout boost-1.57.0  
git submodule update --init  
call "%VS120COMNTOOLS%..\..\vc\vcvarsall.bat" amd64  
bootstrap.bat  
b2 -j8 toolset=msvc-12.0 address-model=64 variant=release,debug link=shared runtime-link=shared windows-api=store  
D:\file\code\lib\boost\boost/asio/detail/buffer_sequence_adapter.hpp(38) : error C2653: 'Windows' : is not a class or namespace name  
D:\file\code\lib\boost\boost/asio/detail/impl/socket_ops.ipp(2246) : error C2871: 'Collections' : a namespace with this name does not exist  
D:\file\code\lib\boost\boost/asio/detail/impl/socket_ops.ipp(2247) : error C2871: 'Networking' : a namespace with this name does not exist  
```  
  
without /ZW there's no Windows namespace or __cplusplus_winrt macro  
  
> https://msdn.microsoft.com/en-us/library/hh561383.aspx  
>   
> The required metadata files, **namespaces**, data types, and functions that your app requires to execute in the Windows Runtime.  
>   
> http://stackoverflow.com/questions/16698949/how-to-access-namespace-windows  
>   
> In the UI, the /ZW option is listed as "Consume Windows Runtime Extension"  
## /ZW is recommended but not necessary for WinRT  
  
another solution is to rewrite boostorg/asio to not require /ZW. which would be pointless  
  
the Windows namespace can be accessed with an #include instead of with /ZW  
  
> http://blogs.msdn.com/b/chuckw/archive/2012/09/18/dual-use-coding-techniques-for-games-part-3.aspx  
>   
> It is, however, possible to be building for a Windows Store app without the /ZW switch (such as in a static library), so the #ifndef __cplusplus_winrt case can still be for a Windows Store app.  
  
Qt is an example of a lib that doesn't use /ZW. but their reason (code standard and text editor support) is pointless  
  
> http://qt-project.org/wiki/WinRT  
>   
> To achieve the best compatibility/consistency with C++ standards as well as support for code editors, only WRL templates [msdn.microsoft.com] should be used when writing platform code for WinRT. No C++/CX extensions should be used.  
>   
> https://github.com/qtproject/qtbase/blob/dev/src/winmain/qtmain_winrt.cpp  
  
```  
#include <wrl.h>  
#include <Windows.ApplicationModel.core.h>  
  
using namespace ABI::Windows::ApplicationModel;  
```  
  
> https://github.com/qtproject/qtbase/search?p=1&q=%2FZW&type=Code&utf8=%E2%9C%93

---

## Comment 1

> Username: eldiener  
> Created_at: 2015-03-20 15:09:13 UTC  
> Url: https://github.com/boostorg/build/pull/62#issuecomment-84039444  

It looks like the pull request adds /ZW to all VC++ compiles. not just WinRT builds. That does not seem to be correct.

---

## Comment 2

> Username: john-peterson  
> Created_at: 2015-03-20 15:27:46 UTC  
> Url: https://github.com/boostorg/build/pull/62#issuecomment-84049200  

fixed

---

## Comment 3

> Username: vprus  
> Created_at: 2015-04-14 09:04:31 UTC  
> Url: https://github.com/boostorg/build/pull/62#issuecomment-92707220  

I had only cursory look, but the title of this pull request and its explanation refers to asio. However, the change itself of for generic Boost.Build code. May I suggest that either this change be made in asio jamfiles, or a comment be added as to why /ZW is reasonable for all uses of Boost.Build?

---

## Comment 4

> Username: john-peterson  
> Created_at: 2015-04-14 13:32:25 UTC  
> Updated_at: 2015-04-14 14:33:15 UTC  
> Url: https://github.com/boostorg/build/pull/62#issuecomment-92837720  

> May I suggest that either this change be made in asio jamfiles,  
  
ya if u tell me where the file is  
  
> or a comment be added as to why /ZW is reasonable for all uses of Boost.Build?  
  
store apps are supposed to be built with /ZW. it's common knowledge. random require /ZW since https://github.com/boostorg/random/commit/1e901ec3bfaf266f8d38e758eb87fbc561831e57

---

## Comment 5

> Username: vprus  
> Created_at: 2015-04-14 13:54:36 UTC  
> Url: https://github.com/boostorg/build/pull/62#issuecomment-92858880  

It might be common, but we have so much common knowledge it that file  
nobody can understand it any longer, so pointers to authoritative  
documents, inside comments, are quite helpful.  
  
On Tue, Apr 14, 2015 at 4:32 PM John Sebastian Peterson <  
notifications@github.com> wrote:  
  
> May I suggest that either this change be made in asio jamfiles,  
>   
> ya if u tell me where the file is  
>   
> or a comment be added as to why /ZW is reasonable for all uses of  
> Boost.Build?  
>   
> store apps are supposed to be built with /ZW. it's common knowledge  
>   
> —  
> Reply to this email directly or view it on GitHub  
> https://github.com/boostorg/build/pull/62#issuecomment-92837720.

---

## Comment 6

> Username: john-peterson  
> Created_at: 2015-04-14 14:06:19 UTC  
> Url: https://github.com/boostorg/build/pull/62#issuecomment-92864629  

there's no clear statement from microsoft. some indirect statements https://www.google.com/search?q=store+apps+compilation+%2Fzw https://github.com/boostorg/build/pull/62#issue-60405609. @chriskohlhoff might know because he wrote a lot of winrt code https://github.com/boostorg/asio/commit/e746865f7882ad29cda8f2bfea10d775839594f5

---

## Comment 7

> Username: eldiener  
> Created_at: 2015-09-09 22:33:32 UTC  
> Url: https://github.com/boostorg/build/pull/62#issuecomment-139063553  

I think this should go through as it will only affect end-users building Windows non-desktop apps as far as the msvc.jam is concerned. That is really little overhead to pay for Boost build users. Is there a reason why we can not move forward with this ?   
  
The link https://msdn.microsoft.com/en-us/library/hh561383.aspx clearly specifies that for Windows Store apps the /ZW switch must always be specified. My understanding of Windows phone apps is that they also use the C++/CX so that adding /ZW for them is also correct. According to the link above the /EHsc compiler switch should always be added when /ZW is added.

---

## Comment 8

> Username: john-peterson  
> Created_at: 2015-09-10 10:45:51 UTC  
> Url: https://github.com/boostorg/build/pull/62#issuecomment-139201903  

i added /EHsc

---

## Comment 9

> Username: vprus  
> Created_at: 2015-09-10 11:08:47 UTC  
> Url: https://github.com/boostorg/build/pull/62#issuecomment-139204955  

I have cherry-picked these two changes (squashing and editing the log message). Thanks!

---

## Comment 10

> Username: mauve  
> Created_at: 2015-10-05 13:41:44 UTC  
> Updated_at: 2015-10-05 13:43:28 UTC  
> Url: https://github.com/boostorg/build/pull/62#issuecomment-145530975  

When I include these changes locally in my boost/build and then try to build boost.filesystem, I get these error messages in VS2015:  
  
```  
 ...failed compile-c-c++ ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\threading-multi\windows-api-store\path_traits.obj...  
 compile-c-c++ ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\threading-multi\windows-api-store\portability.obj portability.cpp  
 ..\..\..\libs\filesystem\src\portability.cpp: fatal error C1107: could not find assembly 'platform.winmd': please specify the assembly search path using /AI or by setting the LIBPATH environment variable  
  
 call "C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat" x86 store >nul  
 cl /Zm800 /ZW /EHsc -nologo @"..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\threading-multi\windows-api-store\portability.obj.rsp"  
  
 ...failed compile-c-c++ ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\threading-multi\windows-api-store\portability.obj...  
 compile-c-c++ ..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\threading-multi\windows-api-store\unique_path.obj unique_path.cpp  
 ..\..\..\libs\filesystem\src\unique_path.cpp: fatal error C1107: could not find assembly 'platform.winmd': please specify the assembly search path using /AI or by setting the LIBPATH environment variable  
  
 call "C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC\vcvarsall.bat" x86 store >nul  
 cl /Zm800 /ZW /EHsc -nologo @"..\..\..\bin.v2\libs\filesystem\build\msvc-14.0\debug\threading-multi\windows-api-store\unique_path.obj.rsp"  
```  
  
Without these changes it compiles fine when invoking `b2 windows-api=store` (after applying my boost.filesystem patches).  
  
From this StackOverflow question and answer (disclaimer answer by me with the help from somebody else) http://stackoverflow.com/questions/32945500/compiling-c-cx-on-the-command-line it seems that `/ZW` requires further options and I also question whether we should use `/ZW` at all in boost (in boost.filesystem I use WRL to call WinRT apis)

---

## Comment 11

> Username: vprus  
> Created_at: 2015-10-05 15:41:00 UTC  
> Url: https://github.com/boostorg/build/pull/62#issuecomment-145574753  

This patch works fine with VS 2013 - where .bat scripts set library paths correctly. The whole issue with VS 2015 seems that .bat scripts look for components in wrong places and fail, and I don't think it's Boost.Build job to work that around. As for /ZW versus WRL, I don't have an opinion. What would be the benefit of using WRL?

---

## Comment 12

> Username: mauve  
> Created_at: 2015-10-05 19:12:53 UTC  
> Url: https://github.com/boostorg/build/pull/62#issuecomment-145635446  

@vprus are you referring to this vs2015 bug you are mentioning here: http://lists.boost.org/Archives/boost/2015/09/225609.php ?  
  
you are right there is probably no benefit of using wrl over c++/cx

---
