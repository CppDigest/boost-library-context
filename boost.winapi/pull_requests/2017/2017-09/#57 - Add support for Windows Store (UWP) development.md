# #57 Add support for Windows Store (UWP) development [Merged]

> Username: jeking3  
> Created at: 2017-09-20 15:39:16 UTC  
> Updated at: 2017-10-07 11:30:51 UTC  
> Merged at: 2017-10-07 11:30:43 UTC  
> Closed at: 2017-10-07 11:30:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/57  

For exapmle: WINAPI_FAMILY=WINAPI_FAMILY_PC_APP  
  
@Lastique My last pull request was taken apart and resubmitted in small chunks, and it caused me to have to rebase a few times.  It took many days to get everything aligned in this.  I would appreciate it if this pull request is merged in whole.  If there are issues, I'd like to try to address them here and update this pull request.  I have downstream work in boost::random that is based on this.  
  
I've found that although the build scripts for winapi do a good job ensuring alignment between the windows headers and the local definitions of things, there may still be some minor incompatibilities that can only be discovered by building a downstream project with it.  
  
For an example of how the new partition macros are to be used, each header defines the partitions that the APIs are allowed in.  To see a downstream project using these, here's a branch with work I have been doing in random:  
  
https://github.com/jeking3/random/blob/header-only-random-device/include/boost/random/detail/random_device_wincrypt.hpp  
  
I added more appveyor CI build jobs to exercise all of the family options in SDKs that support it.  Now there are a lot more jobs, and the CI job is over an hour.  Give the number of compile-time branches, I'm not sure we can avoid this.  We're missing BOOST_NO_ANSI_APIS branches in the CI right now.  
  
The most complex changes were in the file handling area.  
  
I have built the following libraries (without defining WINAPI_FAMILY) locally which use winapi to be sure the changes are compatible:  
  
- chrono  
- date_time  
- dll  
- interprocess  
- log  
- process  
- random  
- sync  
- system  
- thread  
- uuid  
  
Libraries not building properly:  
- stacktrace, however it is not the result of this pull request.  
  
Other notes:  
  
- asio has its own UWP support and could be updated to use winapi  
- config has some WINAPI_FAMILY_PHONE_APP support (disables ANSI APIs) which could be moved to winapi  
- adds a header-only dependency on Boost.Predef  
  
This fixes #53

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-09-20 16:37:03 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-330909566  

Thank you for your work. I only took a quick look, I will review it more closely later at the weekend. I have a few quick notes though:  
  
- test/preflight-uwp.bat is not needed. I have my own set of scripts to run the tests locally, as I'm sure everyone else has for his libraries. No need to store it in git.  
- There are a lot of changes in formatting (indentation), which makes reviewing more difficult. I will also have to re-adjust indentation to the previous style after the merge. I would appreciate if you changed it to be closer to the current code.  
- In page_protection_flags.hpp you condition some of the constants on the SDK version. I don't think this is correct. The constants are supported (or not) by a particular version of Windows, so if anything, it is the target Windows version that should be checked. Windows SDK version may or may not define the constants, which can be considered a bug in the SDK which should worked around by Boost.WinAPI (i.e. if missing, Boost.WinAPI should define the constant).  
- I don't think I understand the rationale behind the `BOOST_WINAPI_UWP_AWARE` macro. Why is it not possible to test for `defined(_INC_WINAPIFAMILY)` directly? Also, non-public (internal to Boost.WinAPI) macros should have `BOOST_DETAIL_WINAPI_` prefix.  
  
Maybe I will have further comments later.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-09-20 16:45:32 UTC  
> Updated_at: 2017-09-20 16:45:42 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-330911708  

> test/preflight-uwp.bat is not needed. I have my own set of scripts to run the tests locally, as I'm sure everyone else has for his libraries. No need to store it in git.  
  
Sounds good, I'll remove it.  
  
> There are a lot of changes in formatting (indentation), which makes reviewing more difficult. I will also have to re-adjust indentation to the previous style after the merge. I would appreciate if you changed it to be closer to the current code.  
  
I'll do that.  The Visual Studio editor wants to indent things and I may not have corrected all of its corrections.  
  
> In page_protection_flags.hpp you condition some of the constants on the SDK version. I don't think this is correct. The constants are supported (or not) by a particular version of Windows, so if anything, it is the target Windows version that should be checked. Windows SDK version may or may not define the constants, which can be considered a bug in the SDK which should worked around by Boost.WinAPI (i.e. if missing, Boost.WinAPI should define the constant).  
  
I'll look that over.  
  
> I don't think I understand the rationale behind the BOOST_WINAPI_UWP_AWARE macro. Why is it not possible to test for defined(_INC_WINAPIFAMILY) directly? Also, non-public (internal to Boost.WinAPI) macros should have BOOST_DETAIL_WINAPI_ prefix.  
  
I struggled for a couple days with macros that contained defined() macros only to find out that the preprocessor behavior is undefined if a macro contains another macro with defined() in it.  Once I split it out like this it got much easier to handle.  I can see if it would be possible to merge it into the partition macros now that I understand why it was failing before.  
  
I expect it'll take a while to review all the changes.  Thanks for the quick look-over.  
  
It would be worthwhile building other boost libraries that use it to make sure they haven't regressed.  I haven't done that except for boost::random so far.

---

## Comment 3

> Username: Lastique  
> Created_at: 2017-09-20 16:54:27 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-330914182  

> I struggled for a couple days with macros that contained defined() macros only to find out that the preprocessor behavior is undefined if a macro contains another macro with defined() in it. Once I split it out like this it got much easier to handle. I can see if it would be possible to merge it into the partition macros now that I understand why it was failing before.  
  
From what I can see, `BOOST_WINAPI_UWP_AWARE` is only used once in that `#if` in config.hpp:20. I don't see it being part of any macros. Did I miss something?

---

## Comment 4

> Username: jeking3  
> Created_at: 2017-09-20 18:14:44 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-330936005  

I see what you mean, I only use the definition in config.hpp so I can eliminate it.

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-09-20 18:28:42 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-330940165  

Regarding page_protection_flags.hpp, Windows SDK 8.0 and 8.1 put WINAPI_FAMILY partitions around it limiting those four definitions to DESKTOP.  If we don't make conditionals based on the SDK version then they will be undefined in BOOST_USE_WINDOWS_H builds and fail.  I'm open for suggestions here; for example we could stop defining them based on the windows values and unconditionally define them whether or not BOOST_USE_WINDOWS_H is defined.  I've seen that done in one other place so I will do that here unless I hear otherwise.

---

## Comment 6

> Username: jeking3  
> Created_at: 2017-09-20 18:42:16 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-330944086  

I have those issues cleaned up and I will force push in a moment.  I should note that file_management.hpp was reorganized quite a bit and the diff isn't going to be pretty, indentation or not.  It was the most complex of the files given there were APIs in three different partitions to deal with.

---

## Comment 7

> Username: Lastique  
> Created_at: 2017-09-20 19:14:43 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-330952589  

On 09/20/17 21:28, James E. King, III wrote:  
> Regarding page_protection_flags.hpp, Windows SDK 8.0 and 8.1 put   
> WINAPI_FAMILY partitions around it limiting those four definitions to   
> DESKTOP. If we don't make conditionals based on the SDK version then   
> they will be undefined in BOOST_USE_WINDOWS_H builds and fail. I'm open   
> for suggestions here; for example we could stop defining them based on   
> the windows values and unconditionally define them whether or not   
> BOOST_USE_WINDOWS_H is defined. I've seen that done in one other place   
> so I will do that here unless I hear otherwise.  
  
Yes, that's what I had in mind as well. Only if those constants are   
limited to desktop we should also them accordingly.

---

## Comment 8

> Username: jeking3  
> Created_at: 2017-09-20 19:20:29 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-330953999  

I have built everything that uses winapi except for stacktrace - for some reason that is failing.  Looking into it now.  I addressed all your other initial comments, I believe.

---

## Comment 9

> Username: jeking3  
> Created_at: 2017-09-20 19:47:04 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-330960505  

The stacktrace failure is different and not caused by this PR so I am ignoring it for now.

---

## Comment 10

> Username: jeking3  
> Created_at: 2017-09-20 22:49:44 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-331002660  

@Lastique would you mind if I put the bcrypt.hpp header into this pull request, or do you want me to keep it separate?

---

## Comment 11

> Username: Lastique  
> Created_at: 2017-09-21 08:33:31 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-331089862  

Let's keep it separate. This one is large enough already.

---

## Comment 12

> Username: Lastique  
> Created_at: 2017-09-23 16:36:36 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-331648582  

Here are some further components after a closer look.  
  
- The current code does have support for non-desktop targets by using Boost.Predef to detect the API partition. I think it would be better to continue this practice instead of defining an alternative mechanism in Boost.WinAPI. See the `BOOST_PLAT_*` macros used in different headers. We can move the boost/predef/platform.h include to config.hpp if it is used in most headers. I realize this requires a lot of changes, but I would rather have a single established approach taken in the whole library.  
  
- There are some new components in this PR that were not in the original code. I would prefer this PR to focus exclusively on UWP support; any new components can be proposed in separate PRs. I'm talking specifically about `MS_DEF_PROV_A_` and `PROV_RNG_` in crypt.hpp, but if there are other additions this concerns those as well. BTW, I don't think adding `MS_DEF_PROV_A_` is a good idea - as a string literal, it can affect binary sizes while it is easily replaced by passing `NULL` instead to `CryptAcquireContext`.  
  
- In config.hpp, don't add " and will not change over time" to the comment. This is not my promise to make (those values come from Windows SDK).  
  
- In a few places there are tokens after `#endif` (there are missing `//` before annotations). See dll.hpp, for instance.  
  
- Don't use `_INC_WINAPIFAMILY`, which is an include guard in winapifamily.h. I don't think this macro name is intended to be stable, even if it is now. You know when the header is included - it defines `WINAPI_FAMILY_PARTITION` and other public macros, you can test those instead.  
  
In order to fix other bugs I will have to commit to Boost.WinAPI. Sorry if that creates inconvenience for you.

---

## Comment 13

> Username: jeking3  
> Created_at: 2017-09-23 21:50:14 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-331672121  

On the crypt additions they leaked in from work on boost random - apologies.  Your attention to detail is high and appreciated in spotting this.  
  
I agree with you on using any existing mechanism to detect UWP.  Let's see if what's in predef is sufficient enough to get the job done.  I was pretty thorough, although I didn't annotate any headers that compiled clean, which probably indicate they are current in all partitions.  There shouldn't be two ways to do this, and I was unaware of this until I was almost done.  
  
I don't mind rebasing on changes, I just didn't want the PR pulled apart and resubmitted in smaller chunks like the others, although I suspect we were both working on the same thing - fixing up what the new CI builds identified as errors.

---

## Comment 14

> Username: jeking3  
> Created_at: 2017-09-25 17:16:30 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-331950057  

Predef does not detect the partition.  It detects the desired family, however we still need to use the partition logic to properly fence off certain API definitions.  
  
Predef will be incorrect in a MinGW build, for example if someone defines WINAPI_FAMILY=WINAPI_FAMILY_PHONE_APP in a MinGW build, BOOST_PLAT_WINDOWS_PHONE will be defined by Predef, even though MinGW has no family support.  
  
The API partitions don't match the families one-for-one, which is why I centered this work around the partition logic used by the SDKs themselves.  
  
I'm going to clean up the remainder of the items identified, and think a little more whether it makes sense to do any work to align with predef, as predef allows code to be conditional on family, but the SDK needs it to be conditional on partition, and the partitions are different in each SDK release from 8.0 to 8.1, and especially in the jump to 10.0.

---

## Comment 15

> Username: jeking3  
> Created_at: 2017-09-25 17:26:06 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-331952748  

It also looks like Predef does not attempt to distinguish MinGW from MinGW-w64, and they are quite different, and most code needs to be conditional around either.

---

## Comment 16

> Username: Lastique  
> Created_at: 2017-09-25 18:16:26 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-331967614  

The way I understand it is that the family requested by the user effectively restricts the set of available APIs. In that sense MinGW doesn't need any explicit support (apart for the family macros) as it always provides the full set of APIs, including those requested by the user. Partitions are just a convenient grouping of the API families.  
  
I think, Boost.WinAPI's job in this perspective is to mimic API families defined by MS Windows SDK. It doesn't preclude it from working with MinGW.

---

## Comment 17

> Username: jeking3  
> Created_at: 2017-09-25 18:28:06 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-331970769  

How would you like to see the winapi header guards change?  Instead of using BOOST_WINAPI_PARTITION_DESKTOP, would you like it to use BOOST_PLAT_WINDOWS_DESKTOP instead?

---

## Comment 18

> Username: Lastique  
> Created_at: 2017-09-25 18:33:52 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-331972405  

Basically, yes. In order to simplify the code in headers Boost.WinAPI could define internal macros equivalent to Windows SDK partirions, only they would be based on Boost.Predef.

---

## Comment 19

> Username: Lastique  
> Created_at: 2017-09-25 18:46:48 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-331976041  

Also, if we're using our internal partition macros we should probably port to those macros the existing code that uses Boost.Predef directly now.

---

## Comment 20

> Username: jeking3  
> Created_at: 2017-09-25 19:38:45 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-331989374  

@Lastique boost.predef is fundamentally broken here.  
Test cpp file:  
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
  
Compile and output if WINAPI_FAMILY not specified is correct:  
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
  
If you define WINAPI_FAMILY however, it is quite broken:  
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
  
I will have to make sure winapifamily.h is included before predef/platform.h inside config.hpp for the platforms that support it (MINGW-w64 and SDK 8.0 or later), and then I think we could make it work.  If someone includes predef/platform.h before winapifamily.h it'll have to be an #error condition.  
  
predef also does not have support for the SYSTEM or SERVER families in it, and the definitions may not line up with SDK 10.0 (looks like it was based on SDK 8.1 so that needs to be looked at in more detail).  I'll try to do these things and submit a second branch called uwp-predef.

---

## Comment 21

> Username: jeking3  
> Created_at: 2017-09-25 20:19:09 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-331999877  

I still prefer using Microsoft supplied partition macros to properly detect the environment, however I made the changes you requested and pushed them, and now winapi relies on predef to determine the family.  Let's see if it builds clean on all CI jobs.  I hope this works, as this all stemmed from a uuid bug that was reported, which brought me to random, which brought me to winapi, which brought me to predef!

---

## Comment 22

> Username: Lastique  
> Created_at: 2017-09-25 22:49:31 UTC  
> Updated_at: 2017-09-25 22:51:23 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-332036036  

It is expected that you cannot define `WINAPI_FAMILY=WINAPI_FAMILY_PHONE_APP` from the command line because `WINAPI_FAMILY_PHONE_APP` does not get expanded before it is defined itself. This is similar to how you currently cannot define `_WIN32_WINNT=_WIN32_WINNT_WINXP` and have to define `_WIN32_WINNT=0x0501` instead.  
  
Including winapifamily.h beforehand is a possible workaround, but if taken, it should be done in Boost.Predef. We can't guarantee that Boost.WinAPI is included first.  
  
> I hope this works, as this all stemmed from a uuid bug that was reported, which brought me to random, which brought me to winapi, which brought me to predef!  
  
Yeah... On the plus side you got yourself acquainted with a lot of Boost code. :)

---

## Comment 23

> Username: jeking3  
> Created_at: 2017-09-25 23:05:13 UTC  
> Updated_at: 2017-09-25 23:05:46 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-332038620  

@Lastique given that the values of these families change from SDK to SDK, defining it as a token that must be expanded is the only way to reliably do it.  That said, the CI builds passed which is nice to see.  Let me know if you have any more review comments on this pull request or if you want me to amend the check-in comment to be a little more elaborate.  
  
I opened an issue in predef for this.

---

## Comment 24

> Username: jeking3  
> Created_at: 2017-09-27 19:50:17 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-332635732  

If predef accepts my pull request (https://github.com/boostorg/predef/pull/57) then I will work to align this pull request with that work once it is in predef/develop.

---

## Comment 25

> Username: jeking3  
> Created_at: 2017-10-04 13:55:30 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-334163107  

@Lastique this is ready for another review.  I changed config.hpp to use predef up front to discover the platform information, and the new edition passed all CI builds.

---

## Comment 26

> Username: Lastique  
> Created_at: 2017-10-07 11:30:51 UTC  
> Url: https://github.com/boostorg/winapi/pull/57#issuecomment-334928682  

Thanks.

---
