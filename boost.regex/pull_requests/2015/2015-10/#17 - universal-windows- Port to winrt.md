# #17 universal-windows: Port to winrt [Closed]

> Username: mauve  
> Created at: 2015-10-04 19:27:05 UTC  
> Updated at: 2015-10-08 11:55:09 UTC  
> Closed at: 2015-10-08 08:50:43 UTC  
> Url: https://github.com/boostorg/regex/pull/17  

This makes fileiter.cpp compile when targeting universal windows (b2 windows-api=store) I still need to port w32_regex_traits for the entire library to build so this PR can wait but I thought I could push it as soon as possible to gather feedback.  
  
UPDATE: everything compiles. w32_regex_traits has been ifdeffed-out though (see commit-message)

---

## Comment 1

> Username: MarcelRaad  
> Created_at: 2015-10-04 20:09:28 UTC  
> Updated_at: 2015-10-06 19:14:47 UTC  
> Url: https://github.com/boostorg/regex/pull/17#discussion_r41101746  

According to the MSDN library documentation, FindFirstFileExW is available in Windows XP and Server 2003, so maybe it can be used unconditionally (like in your filesystem pull request)?  
  
And the boost/predef/platform.h include for the BOOST_PLAT_\* macros seems to be missing, also in your filesystem and test pull requests.

---

## Comment 2

> Username: mauve  
> Created_at: 2015-10-04 20:47:33 UTC  
> Updated_at: 2015-10-06 19:14:47 UTC  
> Url: https://github.com/boostorg/regex/pull/17#discussion_r41102199  

(thanks for the review: @MarcelRaad )  
  
I will switch to unconditionally use FindFirstFileExW.  
  
As it were boost.predef was being pulled in by something else which is the reason why it worked. I guess it is ok to add a dependency to boost.predef?

---

## Comment 3

> Username: mauve  
> Created_at: 2015-10-06 07:18:48 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-145765871  

@MarcelRaad care to take another look?

---

## Comment 4

> Username: MarcelRaad  
> Created_at: 2015-10-06 08:23:45 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-145780909  

Looks good to me!

---

## Comment 5

> Username: mauve  
> Created_at: 2015-10-06 08:28:14 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-145781642  

@MarcelRaad I just pushed a fixed version which re-enables boost::wregex again.

---

## Comment 6

> Username: jzmaddock  
> Created_at: 2015-10-06 12:13:56 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-145838990  

Thanks for the patches, will investigate shortly.

---

## Comment 7

> Username: mauve  
> Created_at: 2015-10-06 12:23:12 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-145840829  

@jzmaddock thanks

---

## Comment 8

> Username: jzmaddock  
> Created_at: 2015-10-06 12:35:42 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-145843086  

I still see errors when building for windows store with msvc-14.0:  
  
```  
compile-c-c++ ..\..\..\bin.v2\libs\regex\build\msvc-14.0\debug\threading-multi\windows-api-store\fileiter.obj  
fileiter.cpp  
..\..\..\libs\regex\build\..\src\fileiter.cpp(108): error C3861: 'CreateFileA': identifier not found  
..\..\..\libs\regex\build\..\src\fileiter.cpp(112): error C3861: 'CreateFileMapping': identifier not found  
..\..\..\libs\regex\build\..\src\fileiter.cpp(121): error C3861: 'MapViewOfFile': identifier not found  
```

---

## Comment 9

> Username: mauve  
> Created_at: 2015-10-06 12:54:21 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-145847888  

@jzmaddock I think you need this in your local boost.config: https://github.com/boostorg/config/pull/80 sorry for not mentioning.

---

## Comment 10

> Username: jzmaddock  
> Created_at: 2015-10-06 16:18:22 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-145917073  

Nope, still get:  
  
```  
compile-c-c++ ..\..\..\bin.v2\libs\regex\build\msvc-14.0\debug\threading-multi\windows-api-store\fileiter.obj  
fileiter.cpp  
..\..\..\libs\regex\build\..\src\fileiter.cpp(112): error C3861: 'CreateFileMapping': identifier not found  
..\..\..\libs\regex\build\..\src\fileiter.cpp(121): error C3861: 'MapViewOfFile': identifier not found  
```  
  
And this page: https://msdn.microsoft.com/en-gb/library/windows/desktop/aa366537%28v=vs.85%29.aspx suggests it's a desktop only API.  The headers seem to confirm that as it's declared inside a #if WINAPI_FAMILY_PARTITION(WINAPI_PARTITION_DESKTOP) block.  
  
Given that your patch disables w32_regex_traits anyway, and the only reason for using the Windows API in fileiter.cpp is to use memory mapped files, there would seem to be a much simpler patch that just involves setting BOOST_REGEX_NO_W32 in regex/config.hpp ??

---

## Comment 11

> Username: mauve  
> Created_at: 2015-10-06 16:21:30 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-145917794  

Ok sorry for wasting your time like this. I will investigate as soon as I have had dinner and spent some time with my kids.

---

## Comment 12

> Username: mauve  
> Created_at: 2015-10-06 19:14:29 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-145969429  

@jzmaddock ok I just tested again:  
  
   1) I checked out the last boost super-module from master  
   2) cd libs/config, git fetch origin, git checkout develop  
   2.1) verify that the following commit is in `config`: `ad3141c - universal-windows: Dont use Windows ANSI APIs when compiling for winrt (2 days ago) <Mikael Olenfalk>`  
   3) cd libs/regex, git checkout mauve/winrt-fileiter  
   4) cd libs/regex/build, ../../../b2 windows-api=store  
   5) Profit!  
  
```  
   Creating library ..\..\..\bin.v2\libs\regex\build\msvc-14.0\debug\threading-multi\windows-api-store\boost_regex-vc140-mt-gd-1_59.lib and object ..\..\..\bin.v2\libs\regex\build\msvc-14.0\debug\threading-multi\windows-api-store\boost_regex-vc140-mt-gd-1_59.exp  
   msvc.manifest.dll ..\..\..\bin.v2\libs\regex\build\msvc-14.0\debug\threading-multi\windows-api-store\boost_regex-vc140-mt-gd-1_59.dll  
   ...updated 15 targets...  
```  
  
I think the problem might have been that my local versions were too old, I have therefore rebase mauve/winrt-fileiter on the latest origin/develop and repushed to this PR again.  
  
Again sorry if I wasted your time.

---

## Comment 13

> Username: MarcelRaad  
> Created_at: 2015-10-06 20:16:48 UTC  
> Updated_at: 2015-10-06 20:19:14 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-145986116  

Works for me too, it's defined in memoryapi.h from the Windows 10 SDK, even when building for Windows 8.0 or 8.1. Perhaps you're using the Windows 8.1 SDK @jzmaddock ? There it's only defined for WINAPI_PARTITION_DESKTOP.  
  
If installed, the Windows 10 SDK should be the default one when building from the command line.

---

## Comment 14

> Username: mauve  
> Created_at: 2015-10-06 21:09:03 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-146001480  

it compiles for me even if I compile with `..\..\..\b2.exe cxxflags=-D_WIN32_WINNT=0x0602` (windows 8) and I don't think we want to require the windows 10 sdk.

---

## Comment 15

> Username: mauve  
> Created_at: 2015-10-07 14:35:00 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-146212809  

I have now verified on a fresh install, in order for this to build you need to have the windows 10 sdk but given that we have set _WIN32_WINNT to 0x0602, the produced binary should still work on windows 8:  
  
`src/tools/msvc.jam:290:feature.compose <windows-api>store : <define>WINAPI_FAMILY=WINAPI_FAMILY_APP <define>_WIN32_WINNT=0x0602`  
  
@jzmaddock how do you want to proceed? is it ok to require the windows 10 sdk for building?

---

## Comment 16

> Username: jzmaddock  
> Created_at: 2015-10-07 17:13:08 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-146265351  

> @jzmaddock https://github.com/jzmaddock how do you want to proceed?   
> is it ok to require the windows 10 sdk for building?  
  
I realise it didn't build before so we're not breaking anything by   
requiring that, but I'm not really happy about requiring a specific SDK.  
  
OK, lets think a little... the only difference between this approach and   
just "disable all windows api usage" is the behaviour of fileiter.cpp   
right?  And that's only used by the deprecated RegEx class, so it seems   
like the simpler option of just not using the Windows API for store   
applications is the way to go?

---

## Comment 17

> Username: mauve  
> Created_at: 2015-10-08 07:19:39 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-146441421  

@jzmaddock ok I didn't know that fileiter was only used by deprecated code, in that case it makes sense to just define BOOST_REGEX_NO_W32 when targeting windows store. I will whip-up a patch.

---

## Comment 18

> Username: mauve  
> Created_at: 2015-10-08 08:50:31 UTC  
> Url: https://github.com/boostorg/regex/pull/17#issuecomment-146461609  

New try: https://github.com/boostorg/regex/pull/18

---
