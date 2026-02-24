# #54 - BOOST_PLAT_WINDOWS_* incorrectly detected [Closed]

> Username: jeking3  
> Created at: 2017-09-25 19:55:39 UTC  
> Updated at: 2017-10-04 03:30:40 UTC  
> Closed at: 2017-10-04 03:30:40 UTC  
> Url: https://github.com/boostorg/predef/issues/54  

I found that on a platform that supports UWP like Visual Studio 2017, if I define WINAPI_FAMILY the current BOOST_PLAT_WINDOWS_* macros produce incorrect results.  
  
Here is my test cpp file:  
  
```  
#include <boost/predef/platform.h>  
  
#define STRING2(X) #X  
#define STRING(X) STRING2(X)  
  
#pragma message("WINAPI_FAMILY")  
#pragma message(STRING(WINAPI_FAMILY))  
  
#pragma message("WINAPI_FAMILY_PHONE_APP")  
#pragma message(STRING(WINAPI_FAMILY_PHONE_APP))  
  
  
#pragma message("BOOST_PLAT_WINDOWS_DESKTOP")  
#pragma message(STRING(BOOST_PLAT_WINDOWS_DESKTOP))  
#if BOOST_PLAT_WINDOWS_DESKTOP  
#pragma message("BOOST_PLAT_WINDOWS_DESKTOP is true")  
#else  
#pragma message("BOOST_PLAT_WINDOWS_DESKTOP is false")  
#endif  
  
#pragma message("BOOST_PLAT_WINDOWS_RUNTIME")  
#pragma message(STRING(BOOST_PLAT_WINDOWS_RUNTIME))  
#if BOOST_PLAT_WINDOWS_RUNTIME  
#pragma message("BOOST_PLAT_WINDOWS_RUNTIME is true")  
#else  
#pragma message("BOOST_PLAT_WINDOWS_RUNTIME is false")  
#endif  
  
#pragma message("BOOST_PLAT_WINDOWS_STORE")  
#pragma message(STRING(BOOST_PLAT_WINDOWS_STORE))  
#if BOOST_PLAT_WINDOWS_STORE  
#pragma message("BOOST_PLAT_WINDOWS_STORE is true")  
#else  
#pragma message("BOOST_PLAT_WINDOWS_STORE is false")  
#endif  
```  
  
Compile and output if WINAPI_FAMILY not specified is correct: it's a desktop build  
```  
c:\Temp>cl /Ic:\boost test.cpp  
Microsoft (R) C/C++ Optimizing Compiler Version 19.11.25507.1 for x64  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
test.cpp  
WINAPI_FAMILY  
WINAPI_FAMILY  
WINAPI_FAMILY_PHONE_APP  
WINAPI_FAMILY_PHONE_APP  
BOOST_PLAT_WINDOWS_DESKTOP  
( (((0)%100)*10000000) + (((0)%100)*100000) + ((1)%100000) )  
BOOST_PLAT_WINDOWS_DESKTOP is true  
BOOST_PLAT_WINDOWS_RUNTIME  
( (((0)%100)*10000000) + (((0)%100)*100000) + ((0)%100000) )  
BOOST_PLAT_WINDOWS_RUNTIME is false  
BOOST_PLAT_WINDOWS_STORE  
( (((0)%100)*10000000) + (((0)%100)*100000) + ((0)%100000) )  
BOOST_PLAT_WINDOWS_STORE is false  
```  
  
If you define WINAPI_FAMILY however, it is broken, thinking all three are true:  
```  
c:\Temp>cl /Ic:\boost test.cpp /DWINAPI_FAMILY=WINAPI_FAMILY_PHONE_APP  
Microsoft (R) C/C++ Optimizing Compiler Version 19.11.25507.1 for x64  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
test.cpp  
WINAPI_FAMILY  
WINAPI_FAMILY_PHONE_APP  
WINAPI_FAMILY_PHONE_APP  
WINAPI_FAMILY_PHONE_APP  
BOOST_PLAT_WINDOWS_DESKTOP  
( (((0)%100)*10000000) + (((0)%100)*100000) + ((1)%100000) )  
BOOST_PLAT_WINDOWS_DESKTOP is true  
BOOST_PLAT_WINDOWS_RUNTIME  
( (((0)%100)*10000000) + (((0)%100)*100000) + ((1)%100000) )  
BOOST_PLAT_WINDOWS_RUNTIME is true  
BOOST_PLAT_WINDOWS_STORE  
( (((0)%100)*10000000) + (((0)%100)*100000) + ((1)%100000) )  
BOOST_PLAT_WINDOWS_STORE is true  
```  
  
You have to force the inclusion of <winapifamily.h> using /FI to fix it:  
```  
c:\Temp>cl /Ic:\boost test.cpp /DWINAPI_FAMILY=WINAPI_FAMILY_PHONE_APP /FIwinapifamily.h  
Microsoft (R) C/C++ Optimizing Compiler Version 19.11.25507.1 for x64  
Copyright (C) Microsoft Corporation.  All rights reserved.  
  
test.cpp  
WINAPI_FAMILY  
3  
WINAPI_FAMILY_PHONE_APP  
3  
BOOST_PLAT_WINDOWS_DESKTOP  
( (((0)%100)*10000000) + (((0)%100)*100000) + ((0)%100000) )  
BOOST_PLAT_WINDOWS_DESKTOP is false  
BOOST_PLAT_WINDOWS_RUNTIME  
( (((0)%100)*10000000) + (((0)%100)*100000) + ((1)%100000) )  
BOOST_PLAT_WINDOWS_RUNTIME is true  
BOOST_PLAT_WINDOWS_STORE  
( (((0)%100)*10000000) + (((0)%100)*100000) + ((0)%100000) )  
BOOST_PLAT_WINDOWS_STORE is false  
```  
  
<winapifamily.h> is not available in all environments however, so you have to be careful about when you include it.  I was trying to update Boost.Winapi for UWP and had defined my own support for UWP, however I was instructed to try to align the work with Boost.Predef, so I gave that a try and ran into this issue, so I thought I would report it.  I have SDK version detection code in [a winapi pull request](https://github.com/boostorg/winapi/pull/57) which can be used to optionally include <winapifamily.h>, given the preprocessor has no notion of "optionally include this file if it exists".  
  
It should be noted that the WINAPI_FAMILY_ constant values have changed from SDK 8.0 to 8.1 to 10.0, so one is required to use the name when defining WINAPI_FAMILY for portability, which leads to this issue.

---

## Comment 1

> Username: jeking3  
> Created at: 2017-09-26 20:07:59 UTC  
> Url: https://github.com/boostorg/predef/issues/54#issuecomment-332319757  

@grafikrobot, @Lastique - properly resolving this issue requires identification of the Windows SDK version in use, and making sure all the variants are handled properly.  I can pull some of the work that was done in winapi over here to ensure that the ``winapifamily.h`` is included if the SDK supports it, and add a test to verify that the various BOOST_PLAT_WINDOWS_* resolve to true or false properly as part of a new test for CI builds to run.    
  
For example with Visual Studio 2010 (toolset=msvc-10.0) which uses SDK 7.0A and has no family support, would we expect BOOST_PLAT_WINDOWS_* to be all false, or would we expect BOOST_PLAT_WINDOWS_DESKTOP to be true, and RUNTIME | PHONE | STORE to be false?  When we add SYSTEM and SERVER from SDK 10.0, what would they be?  
  
For example with Visual Studio 2012 (toolset=msvc-11.0) which uses SDK 8.0, if someone were to specify ``WINAPI_FAMILY=WINAPI_FAMILY_PHONE_APP``, and knowing that value is not introduced until SDK 8.1, would we expect BOOST_PLAT_WINDOWS_PHONE to be false?  
  
Or would we instead attempt to include ``winapifamily.h`` and then define any of the values it has not when compared to the latest SDK 10, and then BOOST_PLAT_WINDOWS_* would always be a reflection of the WINAPI_FAMILY compile-time definition, even if the SDK in question doesn't support that family?

---

## Comment 2

> Username: grafikrobot  
> Created at: 2017-09-26 22:07:40 UTC  
> Url: https://github.com/boostorg/predef/issues/54#issuecomment-332350461  

On Tue, Sep 26, 2017 at 3:08 PM, James E. King, III <  
notifications@github.com> wrote:  
  
> @grafikrobot <https://github.com/grafikrobot>, @Lastique  
> <https://github.com/lastique> - properly resolving this issue requires  
> identification of the Windows SDK version in use, and making sure all the  
> variants are handled properly. I can pull some of the work that was done in  
> winapi over here to ensure that the winapifamily.h is included if the SDK  
> supports it, and add a test to verify that the various BOOST_PLAT_WINDOWS_*  
> resolve to true or false properly as part of a new test for CI builds to  
> run.  
>  
> For example with Visual Studio 2010 (toolset=msvc-10.0) which uses SDK  
> 7.0A and has no family support, would we expect BOOST_PLAT_WINDOWS_* to be  
> all false, or would we expect BOOST_PLAT_WINDOWS_DESKTOP to be true, and  
> RUNTIME | PHONE | STORE to be false? When we add SYSTEM and SERVER from SDK  
> 10.0, what would they be?  
>  
> For example with Visual Studio 2012 (toolset=msvc-11.0) which uses SDK  
> 8.0, if someone were to specify WINAPI_FAMILY=WINAPI_FAMILY_PHONE_APP,  
> and knowing that value is not introduced until SDK 8.1, would we expect  
> BOOST_PLAT_WINDOWS_PHONE to be false?  
>  
I almost understand up to that point. Then I read this.. :-)  
  
> Or would we instead attempt to include winapifamily.h and then define any  
> of the values it has not when compared to the latest SDK 10, and then  
> BOOST_PLAT_WINDOWS_* would always be a reflection of the WINAPI_FAMILY  
> compile-time definition, even if the SDK in question doesn't support that  
> family?  
>  
I prefer for Predef to reflect the "true" state of the compile as available  
from the toolset and environment. Hence I avoid depending on user defined  
behavior. Which I suppose means we must include the winapifamily.h if  
possible. But fall back to other detection.

---

## Comment 3

> Username: jeking3  
> Created at: 2017-09-27 02:25:38 UTC  
> Url: https://github.com/boostorg/predef/issues/54#issuecomment-332390348  

I'll put something together and submit it for comment.

---

## Comment 4

> Username: Lastique  
> Created at: 2017-09-27 11:04:04 UTC  
> Url: https://github.com/boostorg/predef/issues/54#issuecomment-332486517  

> For example with Visual Studio 2010 (toolset=msvc-10.0) which uses SDK 7.0A and has no family support, would we expect BOOST_PLAT_WINDOWS_* to be all false, or would we expect BOOST_PLAT_WINDOWS_DESKTOP to be true, and RUNTIME | PHONE | STORE to be false? When we add SYSTEM and SERVER from SDK 10.0, what would they be?  
  
IIRC, Boost.Predef defines two macros for each feature - the one that indicates the version of the feature (or 0, if the feature is absent) and an `_AVAILABLE` one, which is defined when the feature is available and not defined otherwise. I suppose, when family support is not provided by the Windows SDK, all feature macros should be 0 and the `_AVAILABLE` macros should not be defined. At least, this follows the current protocol, if I'm not mistaken. We could also provide a central macro in Boost.Predef indicating that families are at all supported by the Windows SDK so that users don't have to test for individual families when none are available.  
  
Boost.WinAPI can then test the `_AVAILABLE` macros first to discover which families are available and act accordingly. When families are not supported by Windows SDK, Boost.WinAPI should provide all API, subject only to the target Windows version.  
  
@grafikrobot, this issue requires Boost.Predef to integrate with Windows SDK by including its headers and testing for its macros. I'm not sure how you feel about it, especially since Boost.WinAPI is supposed to do this as well. It is always possible to deprecate and eventually remove the families support from Boost.Predef, and implement the detection logic in Boost.WinAPI instead. I'm fine with either approach; I just don't want us doing the same work twice or having one of the solutions not functional. What do you think, do you want to keep the families support in Boost.Predef?

---

## Comment 5

> Username: jeking3  
> Created at: 2017-09-27 12:23:25 UTC  
> Url: https://github.com/boostorg/predef/issues/54#issuecomment-332503499  

@grafikrobot In working through this I'm looking at the current definition of BOOST_PLAT_WINDOWS_DESKTOP which defines itself available if WINAPI_FAMILY is not defined.  This means on older SDKs like 7.0A it would be defined, and yet there are no families in that SDK.  
  
I have added BOOST_PLAT_WINDOWS_UWP which is defined as available if the development environment has UWP family partition support.  I'm wondering if it makes sense to only define BOOST_WINDOWS_PLAT_* as available iff BOOST_PLAT_WINDOWS_UWP is available?  That's how I'm going to proceed for now, but it's a breaking change for BOOST_PLAT_WINDOWS_DESKTOP in particular which may have been defined in previous boost versions but now it would only be defined if actually enabled and recognized by the Windows SDK being used.

---

## Comment 6

> Username: jeking3  
> Created at: 2017-09-27 12:51:00 UTC  
> Url: https://github.com/boostorg/predef/issues/54#issuecomment-332510177  

``BOOST_PLAT_WINDOWS_RUNTIME`` is being used in some other boost libraries to detect windows in different capacities - modules that don't appear to use winapi.  "Runtime" does not represent a valid UWP family, and I'm wondering if it is really a valid definition for this work.  The modules smart_ptr, system, signal2, chrono, and thread all rely on BOOST_PLAT_WINDOWS_* definitions.

---

## Comment 7

> Username: jeking3  
> Created at: 2017-09-27 15:40:01 UTC  
> Updated at: 2017-09-27 15:47:40 UTC  
> Url: https://github.com/boostorg/predef/issues/54#issuecomment-332563614  

@grafikrobot to properly test changes in CI I need the ability to pass define options into the regression build script ``https://raw.githubusercontent.com/boostorg/regression/develop/ci/src/ci_boost_library_test.py``  
  
It looks like is supports everything except that?  
I think I worked around it by using CXXFLAGS instead.

---

## Comment 8

> Username: jeking3  
> Created at: 2017-09-27 16:15:01 UTC  
> Url: https://github.com/boostorg/predef/issues/54#issuecomment-332574974  

Nope, the build scripts for predef do not allow someone to preflight on appveyor in their own fork.  I will open a new session.  This is something I don't think I can fix, so I will wait until it is.  The appveyor jobs all failed:  https://ci.appveyor.com/project/jeking3/predef/build/1.0.1

---

## Comment 9

> Username: jeking3  
> Created at: 2017-09-29 16:00:00 UTC  
> Url: https://github.com/boostorg/predef/issues/54#issuecomment-333166885  

The pull request is complete and building successfully, please take a look at it.

---

## Comment 10

> Username: grafikrobot  
> Created at: 2017-09-29 16:17:21 UTC  
> Url: https://github.com/boostorg/predef/issues/54#issuecomment-333171091  

On Fri, Sep 29, 2017 at 9:00 AM, James E. King, III <  
notifications@github.com> wrote:  
  
> The pull request is complete and building successfully, please take a look  
> at it.  
>  
Awesome I'll take a look some time today. Since it's complex it will take  
me some time to grok it all though. So it may not be until the weekend when  
I merge it.  
  
  
--   
-- Rene Rivera  
-- Grafik - Don't Assume Anything  
-- Robot Dreams - http://robot-dreams.net  
-- rrivera/acm.org (msn) - grafikrobot/aim,yahoo,skype,efnet,gmail

---

## Comment 11

> Username: jeking3  
> Created at: 2017-10-04 03:30:40 UTC  
> Url: https://github.com/boostorg/predef/issues/54#issuecomment-334040764  

Closing now that https://github.com/boostorg/predef/pull/57 was merged.
