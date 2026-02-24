# #29 Refactored random_device to be concept driven and more easily extensible, and added UWP support [Closed]

> Username: jeking3  
> Created at: 2017-10-07 17:51:20 UTC  
> Updated at: 2017-11-03 18:49:18 UTC  
> Closed at: 2017-11-03 18:17:34 UTC  
> Url: https://github.com/boostorg/random/pull/29  

### Continuous Integration ###  
  
I took #30 and compiled it with this (#29) inside my fork (https://github.com/jeking3/random/pull/3) so I could get build results for this PR.  
  
Appveyor: https://ci.appveyor.com/project/jeking3/random/build/1.0.23-develop  
Travis: https://travis-ci.org/jeking3/random/builds/295933101  
  
### Dependencies ###  
This repository does not yet have CI builds enabled, and this depends on https://github.com/boostorg/winapi/pull/61 which has already been merged.  
  
### Changes ###  
refactored random_device to be concept driven and more easily extensible,  
enabled use in Universal Windows Platform (UWP) development  
  - configurable entropy size  
  - file provider for unix (/dev/urandom)  
  - wincrypt provider for windows desktop targets  
  - bcrypt provider for windows store/phone targets  
  
### Compatibility ###  
  
Backwards compatible with previous random_device implementation.  
  
### Notes ###  
  
Since random_device was the only module not entirely header based, this change converts the library to be fully header-only.  
  
This is also going to be used as part of resolving https://github.com/boostorg/uuid/issues/24, whereby the seed code in uuid will be removed in favor of this.

---

## Comment 1

> Username: swatanabe  
> Created_at: 2017-10-07 19:31:55 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-334960469  

AMDG  
  
- I strongly dislike making things header-only purely for the  
  sake of being header-only.  
- How does this relate to https://github.com/boostorg/random/pull/17  
  
In Christ,  
Steven Watanabe

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-10-07 19:46:27 UTC  
> Updated_at: 2017-10-07 19:52:16 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-334961290  

Hi @swatanabe,  
  
WinRT is quite different from [Universal Windows Platform](https://docs.microsoft.com/en-us/windows/uwp/get-started/universal-application-platform-guide).  Windows fractured the WIN32 API into different partitions based on the target - phone, store, system, server, and desktop.  Boost.WinAPI now supports this behavior, and as the maintainer for uuid I had an open issue where it made more sense to do this work for UWP in random_device() instead.  
  
I asked about header only and the overwhelming response I heard was that header-only is superior, because there are environments where non-header-only libraries are not allowed as part of a project.  
  
What I did here was take the original random_device code and break it up such that the entropy provider is a concept, and there is a file provider for /dev/urandom (or any other file, which the unit test shows); as well as the previous win32 implementation which is not UWP compatible (store, phone partitions do not have WinCrypt libraries); and then a third implementation using BCrypt from Microsoft which works in those partitions.  
  
Given Boost.Uuid is a header-only library, and that it contained similar random seed code to random_device, I decided to make Boost.Random header-only as part of this change because random_device() was the only thing in the library.  Granted random depends on system, and system has libraries, but system can be compiled header-only through a preprocessor macro and that works with random.  
  
To build for windows store you would do:  
``b2 toolset=msvc-14.1 define="_WIN32_WINNT=0x0A00" define="WINAPI_FAMILY=WINAPI_FAMILY_PC_APP"``  
  
or for Windows Phone:  
``b2 toolset=msvc-14.1 define="_WIN32_WINNT=0x0A00" define="WINAPI_FAMILY=WINAPI_FAMILY_PHONE_APP"``  
  
Either of these would cause the bcrypt entropy provider to be used over wincrypt, otherwise wincrypt will still be used so that folks who may have made their own wincrypt drivers for random seed entropy generation in hardware devices can still use those.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-10-12 13:33:41 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-336138591  

@swatanabe any more actions I need to take to move this forward?  I have simplifications to make in Uuid based on the completion of this.

---

## Comment 4

> Username: swatanabe  
> Created_at: 2017-10-12 16:33:24 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-336192734  

AMDG  
  
On 10/07/2017 01:46 PM, James E. King, III wrote:  
> Hi @swatanabe,  
>   
> WinRT is quite different from [Universal Windows Platform](https://docs.microsoft.com/en-us/windows/uwp/get-started/universal-application-platform-guide).  Windows fractured the WIN32 API into different partitions based on the target - phone, store, system, server, and desktop.  Boost.WinAPI now supports this behavior, and as the maintainer for uuid I had an open issue where it made more sense to do this work for UWP in random_device() instead.  
>   
  
  Okay, but you removed the WinRT code from that PR.  
So, does that mean that bcrypt works on WinRT?  
  
> I asked about header only and the overwhelming response I heard was that header-only is superior, because there are environments where non-header-only libraries are not allowed as part of a project.  
>   
  
  Oh, but of course, who needs separate compilation?  Let's  
forget about it and just build every program as a  
single translation unit.  
  
> What I did here was take the original random_device code and break it up such that the entropy provider is a concept, and there is a file provider for /dev/urandom (or any other file, which the unit test shows); as well as the previous win32 implementation which is not UWP compatible (store, phone partitions do not have WinCrypt libraries); and then a third implementation using BCrypt from Microsoft which works in those partitions.  
>   
> Given Boost.Uuid is a header-only library, and that it contained similar random seed code to random_device, I decided to make Boost.Random header-only as part of this change because random_device() was the only thing in the library.  Granted random depends on system, and system has libraries, but system can be compiled header-only through a preprocessor macro and that works with random.  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-10-12 17:13:07 UTC  
> Updated_at: 2017-10-12 17:13:28 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-336204171  

As long as folks are using UWP, defining their _WIN32_WINNT properly and defining their WINAPI_FAMILY properly, and using Visual Studio 2012 or later then they can target different partitions.  If someone wants to develop for Windows Phone they would set WINAPI_FAMILY=WINAPI_FAMILY_PHONE_APP, or for Windows Store WINAPI_FAMILY_PC_APP.  
  
As for WinRT and Windows Phone 8.x development, I am able to build locally using this command which enables the older Windows Runtime / Managed C++ stuff however due to an issue in multiprecision it won't build through to the end, however random_device does build and passes tests:  
```  
c:\boost\libs\random\test>git status  
On branch header-only-random-device  
Your branch is up-to-date with 'origin/header-only-random-device'.  
nothing to commit, working tree clean  
c:\boost\libs\random\test>..\..\..\b2 toolset=msvc-14.1 cxxflags="/ZW /EHsc" variant=debug,release define="_WIN32_WINNT=0x0603" test_random_device  
Performing configuration checks  
  
    - 32-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : no  (cached)  
    - junctions supported      : yes (cached)  
    - hardlinks supported      : yes (cached)  
...patience...  
...found 2217 targets...  
(done, passed)  
  
however trying to build everything:  
  
c:\boost\libs\random\test>..\..\..\b2 toolset=msvc-14.1 cxxflags="/ZW /EHsc" variant=debug,release define="_WIN32_WINNT=0x0603"  
...  
compile-c-c++ ..\..\..\bin.v2\libs\random\test\multiprecision_float_test.test\msvc-14.1\release\threadapi-win32\threading-multi\multiprecision_float_test.obj  
multiprecision_float_test.cpp  
..\..\..\boost/test/unit_test.hpp(61): warning C4447: 'main' signature found without threading model. Consider using 'int main(Platform::Array<Platform::String^>^ args)'.  
..\..\..\boost/multiprecision/detail/functions/constants.hpp(246): error C2482: 'digits': dynamic initialization of thread local data not allowed in WinRT code  
```  
  
I believe that is deprecated in favor of either using [C++/WinRT](https://github.com/Microsoft/cppwinrt) or using UWP.  
  
The presence of WinRT or not should not be relevant here, since the availability of the WinCrypt / BCrypt depends on the target family.  In WinRT one can access Win32 calls and the code provided accesses Wincrypt or BCrypt as appropriate for the target family.

---

## Comment 6

> Username: jeking3  
> Created_at: 2017-10-16 17:15:26 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-336957154  

@swatanabe anything else you need me to do in order to get this merged?

---

## Comment 7

> Username: jeking3  
> Created_at: 2017-10-24 15:45:50 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-339036140  

@swatanabe anything else you need me to do in order to get this merged?  Time is running out on changes for 1.66.0 and I would like to eliminate the seed logic from boost::uuid in favor of this more universal implementation in 1.66.0.

---

## Comment 8

> Username: jeking3  
> Created_at: 2017-10-30 10:38:27 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-340406162  

@swatanabe I hate the keep repeating myself, but is there anything else you need me to do in order to get this merged? Time is running out on changes for 1.66.0 and I would like to eliminate random seed logic from boost::uuid (it doesn't belong there!) in favor of this more universal implementation in 1.66.0.

---

## Comment 9

> Username: jeking3  
> Created_at: 2017-11-01 01:29:51 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-340952906  

To address your concerns regarding header-only:  
  
1. Feedback I got before I started on this was that header-only is preferred, as some projects will only allow header-only modules.  
2. Boost.Uuid has an erroneously placed class "seed_rng" which is header-only so I knew it was possible to do it this way.  
3. Refactoring random_device simplified the code, and the header-only implementation is small enough to be reasonable for templates.  
4. It is possible to use Boost.System in header-only mode with a macro, therefore it is advantageous to allow Boost.Random to be header-only.  
  
You haven't gotten back to me on what the specific hold-up is on this pull request, so I'm trying to infer what the delay is about, and based on your responses this is the only thing I can think of.  Please work with me to resolve this issue, as it is blocking a defect fix in Boost.Uuid.  In fact, I am going to remove seed_rng from Boost.Uuid once this merges.  As it is, I may not be able to get changes into 1.66.0, but I would like to try.  
  
If there's a specific problem with the refactored random_device I would like to know.

---

## Comment 10

> Username: swatanabe  
> Created_at: 2017-11-01 17:10:12 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-341172162  

AMDG  
  
On 10/31/2017 07:29 PM, James E. King, III wrote:  
> To address your concerns regarding header-only:  
>   
> 1. Feedback I got before I started on this was that header-only is preferred, as some projects will only allow header-only modules.  
  
  That's a really silly policy, then.  Having  
the implementation in headers is the price  
of using templates.  It's hardly a good thing  
in itself.  
  
> 2. Boost.Uuid has an erroneously placed class "seed_rng" which is header-only so I knew it was possible to do it this way.  
  
Of course, it's possible.  That doesn't make it a good idea.  
  
> 3. Refactoring random_device simplified the code, and the header-only implementation is small enough to be reasonable for templates.  
  
Huh?  
  
> 4. It is possible to use Boost.System in header-only mode with a macro, therefore it is advantageous to allow Boost.Random to be header-only.  
>   
  
  I don't see how that follows.  To be honest, I consider  
making a library optionally header-only to be the worst  
possible choice as it creates more ways to have incompatible  
build options for no good reason.  
  
> You haven't gotten back to me on what the specific hold-up is on this pull request, so I'm trying to infer what the delay is about, and based on your responses this is the only thing I can think of.  Please work with me to resolve this issue, as it is blocking a defect fix in Boost.Uuid.  In fact, I am going to remove seed_rng from Boost.Uuid once this merges.  As it is, I may not be able to get changes into 1.66.0, but I would like to try.  
>   
> If there's a specific problem with the refactored random_device I would like to know.  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 11

> Username: jeking3  
> Created_at: 2017-11-01 18:10:24 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-341191572  

You still haven't provided me with guidance on what needs to happen in order to get this accepted.  Do I need to make it library based again?  Is there anything else I need to do other than that?

---

## Comment 12

> Username: jeking3  
> Created_at: 2017-11-01 18:37:01 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-341199211  

I see no good way to go back to a library based approach without losing the RandomDeviceProvider concept, and instead jamming the refactoring work that was done to separate the three distinct types out into templatized headers back into a single .cpp file with macro separation.  Isn't that inferior?  The previous implementation had preprocessor macros to define platform separations.  Now there's a clean interface and per-platform implementations in their own file.  It is more maintainable and easier to extend now.  Each platform specific implementation is around 2KB with comments and has a small concept to understand.  For example to add CloudABI support, one needs only to add another detail file for CloudABI and then use the proper Boost.Predef detector to make it the default choice in random_device.hpp for CloudABI.  
  
I'm not really understanding the reason why random_device in particular should NOT be header-only.  Looking at the other implementations that are templates, like mersenne_twister, they are much larger and more complex than random_device and they are not in the library.  
  
I think the most important point here is that this is an improvement.  The fact that it converted the only piece of logic in the Boost.Random library into header-only is a side-effect of following the norms found throughout other boost libraries - the adoption of concepts over inheritence.  If there was anything else in the src/ directory that needed to be there, it would still be library based, but now it doesn't need to be and that too is an improvement.  It feels like the work I did is being unfairly judged because it also removed the last piece of library based source from Boost.Random.  That is not my fault.  
  
In summary, all I want to do is fix UWP support in random_device so that I can remove seed_rng from Boost.Uuid.  Since you are the maintainer, if you say that I need to start over and jam bcrypt support into a third set of preprocessor-macro separated code in random_device then fine, I will do that, but I don't think it's the right decision.  So please let me know so I can make this happen, whatever the direction.

---

## Comment 13

> Username: jeking3  
> Created_at: 2017-11-01 19:18:32 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-341210888  

I will add WinRT support identical to what was found before, leveraging the proposed header framework.

---

## Comment 14

> Username: jeking3  
> Created_at: 2017-11-01 19:53:27 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-341220824  

Actually, I am not going to add WinRT support in the same manner that existed before.  First of all, if you build with `/ZW` enabled on the current develop branch, it is broken:  
```  
c:\boost\libs\random\test>..\..\..\b2 toolset=msvc-14.1 cxxflags="/ZW" -q test_random_device  
Performing configuration checks  
  
    - 32-bit                   : yes (cached)  
    - arm                      : no  (cached)  
    - mips1                    : no  (cached)  
    - power                    : no  (cached)  
    - sparc                    : no  (cached)  
    - x86                      : yes (cached)  
    - symlinks supported       : no  
    - junctions supported      : yes  
    - hardlinks supported      : yes  
...patience...  
...found 1996 targets...  
...updating 18 targets...  
common.mkdir ..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static  
common.mkdir ..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32  
common.mkdir ..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi  
compile-c-c++ ..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\test_random_device.obj  
test_random_device.cpp  
c:\boost\boost/test/impl/unit_test_main.ipp(273): warning C4447: 'main' signature found without threading model. Consider using 'int main(Platform::Array<Platform::String^>^ args)'.  
common.mkdir ..\..\..\bin.v2\libs\random\build\msvc-14.1\debug\link-static  
common.mkdir ..\..\..\bin.v2\libs\random\build\msvc-14.1\debug\link-static\threadapi-win32  
common.mkdir ..\..\..\bin.v2\libs\random\build\msvc-14.1\debug\link-static\threadapi-win32\threading-multi  
compile-c-c++ ..\..\..\bin.v2\libs\random\build\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\random_device.obj  
random_device.cpp  
msvc.archive ..\..\..\bin.v2\libs\random\build\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\libboost_random-vc141-mt-gd-1_66.lib  
..\..\..\bin.v2\libs\random\build\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\random_device.obj : warning LNK4264: archiving object file compiled with /ZW into a static library; note that when authoring Windows Runtime types it is not recommended to link with a static library that contains Windows Runtime metadata unless /WHOLEARCHIVE is specified to include everything from the static library  
common.mkdir ..\..\..\bin.v2\libs\system\build\msvc-14.1\debug\link-static  
common.mkdir ..\..\..\bin.v2\libs\system\build\msvc-14.1\debug\link-static\threadapi-win32  
common.mkdir ..\..\..\bin.v2\libs\system\build\msvc-14.1\debug\link-static\threadapi-win32\threading-multi  
compile-c-c++ ..\..\..\bin.v2\libs\system\build\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\error_code.obj  
error_code.cpp  
msvc.archive ..\..\..\bin.v2\libs\system\build\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\libboost_system-vc141-mt-gd-1_66.lib  
..\..\..\bin.v2\libs\system\build\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\error_code.obj : warning LNK4264: archiving object file compiled with /ZW into a static library; note that when authoring Windows Runtime types it is not recommended to link with a static library that contains Windows Runtime metadata unless /WHOLEARCHIVE is specified to include everything from the static library  
msvc.link ..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\test_random_device.exe  
msvc.manifest ..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\test_random_device.exe  
testing.capture-output ..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\test_random_device.run  
====== BEGIN OUTPUT ======  
Running 1 test case...  
unknown location(0): fatal error: in "test_main_caller(_argc,_argv_)": unknown type  
test_random_device.cpp(19): last checkpoint  
  
*** 1 failure is detected in the test module "Test Program"  
  
EXIT STATUS: 201  
====== END OUTPUT ======  
  
  
    set status=0  
    if %status% NEQ 0 (  
        echo Skipping test execution due to testing.execute=off  
        exit 0  
    )  
     "..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\test_random_device.exe"   > "..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\test_random_device.output" 2>&1  
    set status=%ERRORLEVEL%  
    echo. >> "..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\test_random_device.output"  
    echo EXIT STATUS: %status% >> "..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\test_random_device.output"  
    if %status% EQU 0 (  
        copy "..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\test_random_device.output" "..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\test_random_device.run"  
    )  
    set verbose=0  
    if %status% NEQ 0 (  
        set verbose=1  
    )  
    if %verbose% EQU 1 (  
        echo ====== BEGIN OUTPUT ======  
        type "..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\test_random_device.output"  
        echo ====== END OUTPUT ======  
    )  
    exit %status%  
  
...failed testing.capture-output ..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threadapi-win32\threading-multi\test_random_device.run...  
...failed updating 1 target...  
...skipped 1 target...  
...updated 16 targets...  
  
c:\boost\libs\random\test>  
```  
  
The code I provided in Boost.Predef to detect the WINAPI_FAMILY coupled with the fact that Win32 APIs are available when building in Managed C++ mode means the previous code blocks for `BOOST_RANDOM_WINDOWS_RUNTIME` are not necessary, and since they are broken anyway, this solves two problems at once.  It provides proper UWP support and removes the implementation that doesn't work.

---

## Comment 15

> Username: jeking3  
> Created_at: 2017-11-01 21:35:28 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-341250340  

This is ready once again for consideration as all CI builds passed.  We cannot enable UWP CI builds because Boost.Test is not ready for it yet - it needs to be moved to Boost.WinAPI instead of referencing the Win32 API directly before additional UWP based CI builds can be added to #30.

---

## Comment 16

> Username: swatanabe  
> Created_at: 2017-11-03 17:50:30 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-341778875  

AMDG  
  
On 11/01/2017 12:37 PM, James E. King, III wrote:  
> I see no good way to go back to a library based approach without losing the RandomDeviceProvider concept, and instead jamming the refactoring work that was done to separate the three distinct types out into templatized headers back into a single .cpp file with macro separation.  Isn't that inferior?  
  
  I don't see any benefit to the RandomDeviceProvider concept.  
It's not like anyone will want to use different implementations  
in different parts of a program.  Even if someone did, the correct  
concept is UniformRandomNumberGenerator.  
  
>  The previous implementation had preprocessor macros to define platform separations.  Now there's a clean interface and per-platform implementations in their own file.  It is more maintainable and easier to extend now.  Each platform specific implementation is around 2KB with comments and has a small concept to understand.  For example to add CloudABI support, one needs only to add another detail file for CloudABI and then use the proper Boost.Predef detector to make it the default choice in random_device.hpp for CloudABI.  
>  
  
  Whether it's in one file or several files makes no real  
difference, not to mention that this is completely orthogonal  
to whether it's defined in the header or is separately compiled.  
  
> I'm not really understanding the reason why random_device in particular should NOT be header-only.  Looking at the other implementations that are templates, like mersenne_twister, they are much larger and more complex than random_device and they are not in the library.  
>   
  
  You're asking the question backwards.  In general, code  
should not be defined in headers unless it needs to be there.  
mersenne_twister has to be defined in the header because it's  
a template.  random_device is not a template, and the function  
call overhead is almost certainly insignificant, so there's  
no reason to inline it either.  
  Also, I prefer to separately compile anything that needs  
to #include system headers, as system headers tend to pollute  
the global namespace.  (Admittedly, the worst in that regard  
is windows.h, which is now worked around by boost/winapi).  
  
> I think the most important point here is that this is an improvement.  The fact that it converted the only piece of logic in the Boost.Random library into header-only is a side-effect of following the norms found throughout other boost libraries - the adoption of concepts over inheritence.  
  
  What inheritance?  There's no reason for either inheritance  
or concepts when you're just defining a single concrete class.  
  
>  If there was anything else in the src/ directory that needed to be there, it would still be library based, but now it doesn't need to be and that too is an improvement.  It feels like the work I did is being unfairly judged because it also removed the last piece of library based source from Boost.Random.  That is not my fault.  
>   
  
  I would object even more strongly if there were other  
components in the library.  Not having the library does  
add a certain amount of convenience, even if I think it's  
ill-advised.  If you couldn't even remove the library,  
there would be no benefit at all to making random_device  
header-only.  
  
> In summary, all I want to do is fix UWP support in random_device so that I can remove seed_rng from Boost.Uuid.  Since you are the maintainer, if you say that I need to start over and jam bcrypt support into a third set of preprocessor-macro separated code in random_device then fine, I will do that, but I don't think it's the right decision.  So please let me know so I can make this happen, whatever the direction.  
>   
  
In Christ,  
Steven Watanabe

---

## Comment 17

> Username: jeking3  
> Created_at: 2017-11-03 18:17:34 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-341786238  

Originally I was just going to remove seed_rng.hpp from Boost.Uuid entirely however I was a very new maintainer at the time and was given guidance that this would make it so that Boost.Uuid would no longer be considered "header only" so I worked to making Boost.Random header-only.  It looks like that was misguided, so I will go and remove seed_rng.hpp from Boost.Uuid and make sure the release notes indicate the new dependency on Boost.Random.  
  
Separately, if you want an implementation of random_device that works on UWP that is not header-only, it would be a good idea to follow the same strategy of separating each platform implementation into a separate file.  I tried to jam bcrypt into the existing file and it got pretty messy with the windows/not_bcrypt/bcrypt/winrt all in there together.

---

## Comment 18

> Username: swatanabe  
> Created_at: 2017-11-03 18:49:18 UTC  
> Url: https://github.com/boostorg/random/pull/29#issuecomment-341794481  

AMDG  
  
On 11/03/2017 12:17 PM, James E. King, III wrote:  
> Separately, if you want an implementation of random_device that works on UWP that is not header-only, it would be a good idea to follow the same strategy of separating each platform implementation into a separate file.  I tried to jam bcrypt into the existing file and it got pretty messy with the windows/not_bcrypt/bcrypt/winrt all in there together.  
>   
  
  That seems reasonable.  When I took over Boost.Random,  
there was only one implementation (/dev/[u]random).  
I originally added a single preprocessor switch (windows  
vs. unix), which was still manageable for a single file.  
  
In Christ,  
Steven Watanabe

---
