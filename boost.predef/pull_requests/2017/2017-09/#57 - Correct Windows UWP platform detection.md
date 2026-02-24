# #57 Correct Windows UWP platform detection [Merged]

> Username: jeking3  
> Created at: 2017-09-27 16:55:28 UTC  
> Updated at: 2017-10-16 03:51:35 UTC  
> Merged at: 2017-10-04 01:36:18 UTC  
> Closed at: 2017-10-04 01:36:18 UTC  
> Url: https://github.com/boostorg/predef/pull/57  

Based on discussions in #54, the BOOST_PLAT_WINDOWS UWP definitions will only be available if the platform, toolset, and build-time options enable them.  This pull request also addresses #55 by adding detection support for ``WINAPI_FAMILY_SERVER`` and ``WINAPI_FAMILY_SYSTEM``.  
  
I was unable to run the CI builds in my fork due to #56.  It looks like appveyor is running for this repository on pull requests so we'll get some good results from that.

---

## Review 1 [Changes requested]

> Username: grafikrobot  
> Created_at: 2017-10-03 15:13:47 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/predef/pull/57#pullrequestreview-66790546  

📁 include/boost/predef/platform/windows_uwp.h

```diff
  48 |+ #else
  49 |+ #   include <ntverp.h>          // Windows SDK
  50 |+ #   define BOOST_PLAT_WINDOWS_SDK_VERSION VER_PRODUCTBUILD
```

> Username: grafikrobot  
> Created_at: 2017-10-03 15:09:33 UTC  
> Updated_at: 2017-10-04 00:06:53 UTC  
> Url: https://github.com/boostorg/predef/pull/57#discussion_r142430643  

This needs to be encoded in the `BOOST_VERSION_NUMBER` format, or just be `BOOST_VERSION_NUMBER_AVAILABLE`. As we want to avoid coding in the "internal" number formats. I understand there's only a build number on this one. But that fine to just have that.

---

📁 include/boost/predef/platform/windows_uwp.h

```diff
  52 |+ 
  53 |+ // 9200 is Windows SDK 8.0 from ntverp.h which introduced family support
  54 |+ #if (BOOST_PLAT_WINDOWS_SDK_VERSION >= 9200) || (defined(__MINGW64__) && __MINGW64_VERSION_MAJOR >= 3)
```

> Username: grafikrobot  
> Created_at: 2017-10-03 15:12:25 UTC  
> Updated_at: 2017-10-04 00:06:53 UTC  
> Url: https://github.com/boostorg/predef/pull/57#discussion_r142431478  

When `BOOST_PLAT_WINDOWS_SDK_VERSION` is encoded as a `BOOST_VERSION_NUMBER` this should also use the standard vnum.


---

## Comment 2

> Username: jeking3  
> Created_at: 2017-10-04 00:07:26 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334014580  

I corrected the issues, please review again once the CI build completes.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-10-04 03:13:14 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334038898  

Thanks, I refreshed my other two pull requests against the new develop.  
@lastique I will rework the winapi pull request for UWP based on this.

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-10-06 10:01:02 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334712890  

Looks like it broke Boost.Thread on MinGW (http://www.boost.org/development/tests/develop/developer/thread.html) and MSVC-9, 10 (https://ci.appveyor.com/project/viboes/thread/build/1.0.132-develop/job/rkeatfxdrkjebtpt). The problem seems to be that neither `BOOST_PLAT_WINDOWS_RUNTIME` nor `BOOST_PLAT_WINDOWS_DESKTOP` is enabled, which makes Boost.WinAPI hide `GetTickCount` and Boost.Thread still use it.  
  
This is something we discussed in https://github.com/boostorg/predef/issues/54, so the fix is probably to update Boost.WinAPI to the new behavior, but this is clearly a breaking change. Maybe we should announce this on the ML and in 1.66 release notes.

---

## Comment 5

> Username: jeking3  
> Created_at: 2017-10-06 12:34:30 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334742022  

Boost.WinAPI already has the new behavior fixed in the pull request for UWP that I refactored to use this work.  It just needs to be merged.    
  
https://github.com/boostorg/winapi/pull/57  
  
I agree this is a breaking change for SDKs that do not have <winapifamily.h> which means MinGW (original) and MSVC 10.0 or earlier.  In those environments ``BOOST_PLAT_WINDOWS_RUNTIME`` used to be defined and now it will not be.  I wonder if it would be better to say, "if !BOOST_PLAT_WINDOWS_UWP then define BOOST_PLAT_WINDOWS_RUNTIME" to maintain backwards compatibility, and BOOST_PLAT_WINDOWS_RUNTIME can be removed in the next release?

---

## Comment 6

> Username: Lastique  
> Created_at: 2017-10-06 12:40:02 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334743202  

The breaking change is that `BOOST_PLAT_WINDOWS_DESKTOP` was previously 1 on older SDKs, which resulted in `GetTickCount` being defined and now it's 0.  
  
I will take a look at the Boost.WinAPI PR as soon as I can.

---

## Comment 7

> Username: jeking3  
> Created_at: 2017-10-06 13:29:14 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334754777  

Ah yes, well, I would prefer we leave it the way it is then, and make sure folks know that difference.  Thanks for correcting me on that.

---

## Comment 8

> Username: jeking3  
> Created_at: 2017-10-06 20:33:58 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334863064  

I can confirm Boost.Thread is broken by this change.  It relies on the ``BOOST_PLAT_WINDOWS_RUNTIME`` which no longer evaluates to non-zero in older SDKs.  Here's a not-definitive list of the affected libraries:  
  
```  
jking@ubuntu:~/boost$ find /home/jking/boost/libs -type f -name '*.h*' -exec grep -l 'BOOST_PLAT_WINDOWS_' {} \; | grep -v 'winapi' | grep -v 'predef'  
/home/jking/boost/libs/smart_ptr/include/boost/smart_ptr/detail/yield_k.hpp  
/home/jking/boost/libs/smart_ptr/include/boost/smart_ptr/detail/lwm_win32_cs.hpp  
/home/jking/boost/libs/thread/include/boost/thread/win32/thread_primitives.hpp  
/home/jking/boost/libs/thread/include/boost/thread/win32/thread_data.hpp  
/home/jking/boost/libs/regex/include/boost/regex/config.hpp  
/home/jking/boost/libs/signals2/include/boost/signals2/detail/lwm_win32_cs.hpp  
/home/jking/boost/libs/chrono/include/boost/chrono/process_cpu_clocks.hpp  
/home/jking/boost/libs/chrono/include/boost/chrono/detail/inlined/win/process_cpu_clocks.hpp  
/home/jking/boost/libs/chrono/include/boost/chrono/config.hpp  
/home/jking/boost/libs/chrono/include/boost/chrono/io/time_point_units.hpp  
/home/jking/boost/libs/chrono/stopwatches/include/boost/chrono/stopwatches/reporters/process_default_formatter.hpp  
/home/jking/boost/libs/chrono/stopwatches/include/boost/chrono/stopwatches/reporters/laps_stopwatch_default_formatter.hpp  
/home/jking/boost/libs/chrono/stopwatches/include/boost/chrono/stopwatches/simple_stopwatch.hpp  
/home/jking/boost/libs/chrono/stopwatches/include/boost/chrono/stopwatches/strict_stopwatch.hpp  
```  
  
Each of these is going to need some attention to properly align to the windows platform detection.  In some cases folks may just need to check for ``BOOST_OS_WINDOWS`` and in others they might need to limit to a specific ``BOOST_PLAT_WINDOWS_*``.  
  
How do you want me to handle this, since I introduced the breaking change?  Should I send a message to the list and indicate what the change was, and why it was made, and let folks know I would be submitting pull requests into the affected projects to remedy it (if they don't beat me to it, that is)?  
  
Alternatively, should we consider leaving the old detection macros in place and deprecate them, and introduce a new set of macros?  For example all of the new ones could be ``BOOST_PLAT_UWP_*`` instead.  This would give folks enough time to transition.  Thoughts?  I would be happy to submit another PR to help smooth out the transition.  I'm not particularly concerned about the boost libraries and aligning them to the new macros, but I am concerned about consuming applications and what effort it will impose on others.  @grafikrobot what are your thoughts on this?

---

## Comment 9

> Username: viboes  
> Created_at: 2017-10-07 08:06:14 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334918320  

For Boost.Thread I will accept a PR.  
Even if WinApi is internal to Boost, I suspect that there external users, so yes a deprecation period would be the good choice.

---

## Comment 10

> Username: pdimov  
> Created_at: 2017-10-07 16:31:44 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334947677  

The usual practice here is to not break things in other libraries that have worked for years without a *very* good reason. So please try to refrain from doing so.

---

## Comment 11

> Username: pdimov  
> Created_at: 2017-10-07 17:25:46 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334952072  

Copying my comment from :  
  
https://github.com/boostorg/winapi/issues/60  
  
The problem is that the old PLAT macros were mutually exclusive and indicated platforms, not API families. Changing them to mean (overlapping) API families is a breaking change, as code relies on their being mutually exclusive.  
  
A much more prudent approach would have been to not touch those and introduce separate FAMILY macros which new code can test.

---

## Comment 12

> Username: Lastique  
> Created_at: 2017-10-07 17:29:19 UTC  
> Updated_at: 2017-10-07 17:29:36 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334952323  

Also from there:  
  
Families are not overlapping in the sense that the user selects only one family. The Boost.Predef macros directly derive from `WINAPI_FAMILY`, which makes them indicate the family selected by the user or Windows SDK. In this light I don't see how these new FAMILY macros would differ from the existing ones.

---

## Comment 13

> Username: pdimov  
> Created_at: 2017-10-07 17:53:02 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334954112  

They would differ in not breaking the assumptions old code makes when using the PLAT macros. `BOOST_PLAT_WINDOWS_DESKTOP` had a specific meaning, which has now changed; and similarly, `!BOOST_PLAT_WINDOWS_RUNTIME` also had a specific meaning, which has now also changed. These changes are completely unnecessary and are only caused by reusing these macros to now mean "family".

---

## Comment 14

> Username: jeking3  
> Created_at: 2017-10-07 19:06:46 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334958865  

We could modify this work to leave the previous ``BOOST_PLAT_WINDOWS_*`` intact exactly as they were before, but deprecated, and move the new ones to ``BOOST_PLAT_UWP_*`` (moving ``BOOST_PLAT_WINDOWS_UWP`` to ``BOOST_PLAT_UWP``).  My only concern with that is that if someone does enable WINAPI_FAMILY in a build, any existing boost library using the older macros may fail to compile, however I agree it would not be a breaking change for older SDKs (MinGW original and msvc-10.0 and earlier) We are talking about SDKs that are over 5 years old however.  Hopefully they are not widely used at this point?  
  
Deprecation versus replacement was discussed at length in #54.  If folks can all agree one way or the other, I'll make changes if necessary.  It may not be for a few days, as I'm traveling the next couple of days.

---

## Comment 15

> Username: pdimov  
> Created_at: 2017-10-07 19:57:29 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334961910  

It would be more intuitive if we call the new macros `BOOST_UWP_FAMILY_*`, reflecting the new terminology.  
  
What the old macros need to be set to when new code enables `WINAPI_FAMILY` is up to us as well. We can set them to whatever makes most sense and is least breaking.

---

## Comment 16

> Username: jeking3  
> Created_at: 2017-10-07 20:37:49 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334964223  

@grafikrobot, @Lastique any comments on this?  I think we would either deprecate the BOOST_PLAT_WINDOWS macros or use BOOST_PLAT_UWP_* as these are platform concerns.

---

## Comment 17

> Username: pdimov  
> Created_at: 2017-10-07 20:41:55 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334964457  

On second thought, yes, `BOOST_PLAT_UWP_*` sounds good, as we have to observe the `BOOST_PLAT_` prefix.

---

## Comment 18

> Username: pdimov  
> Created_at: 2017-10-07 20:58:00 UTC  
> Updated_at: 2017-10-07 21:05:42 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334965274  

On third thought, I take my suggestion back. We just need to leave `BOOST_PLAT_WINDOWS_DESKTOP` defined to 1 when a family is not explicitly requested or the SDK isn't UWP, as was the case before this patch.

---

## Comment 19

> Username: Lastique  
> Created_at: 2017-10-07 22:28:26 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334970010  

@pdimov There really isn't any "other" meaning about the `BOOST_PLAT_WINDOWS_DESKTOP` or `BOOST_PLAT_WINDOWS_RUNTIME` macros. They always reflected the `WINAPI_FAMILY` value (see http://www.boost.org/doc/libs/1_65_1/doc/html/predef/reference.html#predef.reference.boost_plat_platform_macros). That they are mutually exclusive is a pure coincidence that could potentially change (e.g. when MS changes its definition of the families - again).  
  
The only thing that changed is the default when `WINAPI_FAMILY` is not defined (which is likely when SDK does not support families). A new set of macros will just do the same, only with a different default. I'm opposed to having two equivalent sets of macros for the same purpose, it only adds confusion. If we really want the old default, let's just restore it (the default) and be done with it.  
  
Also, I'm a bit reluctant to have to rework the support for families in Boost.WinAPI.

---

## Comment 20

> Username: Lastique  
> Created_at: 2017-10-07 22:35:19 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334970308  

> That they are mutually exclusive is a pure coincidence that could potentially change (e.g. when MS changes its definition of the families - again).  
  
Actually, I don't think they are mutually exclusive even now. Neither of them takes into account `WINAPI_FAMILY_SYSTEM`, for example.

---

## Comment 21

> Username: pdimov  
> Created_at: 2017-10-07 22:54:20 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334971092  

> The only thing that changed is the default when `WINAPI_FAMILY` is not defined  
  
Yes, exactly. See "on third thought."  
  
In short, every use of `BOOST_PLAT_WINDOWS_DESKTOP` is now broken by default, but there's no need for that.  
  
`BOOST_PLAT_WINDOWS_RUNTIME` uses still seem fine after the change.

---

## Comment 22

> Username: jeking3  
> Created_at: 2017-10-07 22:59:40 UTC  
> Updated_at: 2017-10-07 23:00:03 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334971319  

There are more issues throughout boost related to proper UWP development.  This is really the first step.  
For example if I build Boost.Random with ``WINAPI_FAMILY_PC_APP`` I see build errors in Boost.Test:  
```  
compile-c-c++ ..\..\..\bin.v2\libs\random\test\test_random_device.test\msvc-14.1\debug\link-static\threading-multi\test_random_device.obj  
test_random_device.cpp  
c:\boost\boost/test/impl/debug.ipp(691): error C3861: 'DebugBreak': identifier not found  
c:\boost\boost/test/impl/debug.ipp(693): error C3861: 'UnhandledExceptionFilter': identifier not found  
c:\boost\boost/test/impl/debug.ipp(693): error C2184: 'unknown-type': illegal type for __except expression  
c:\boost\boost/test/impl/debug.ipp(707): error C3861: 'DebugBreak': identifier not found  
c:\boost\boost/test/impl/debug.ipp(849): error C2065: 'STARTUPINFOA': undeclared identifier  
```  
  
This is because Boost.Test is not using Boost.WinAPI.  Similarly, if I use ``WINAPI_FAMILY_SYSTEM`` I see:  
```  
c:\boost\boost/test/impl/debug.ipp(853): error C2065: 'STARTF_USESHOWWINDOW': undeclared identifier  
```  
  
Each of the Boost libraries is going to need a sanity pass with the different families defined, and convert them to use WinAPI - if we want the entire suite to work for UWP development.

---

## Comment 23

> Username: pdimov  
> Created_at: 2017-10-07 23:08:10 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334971647  

Sure, my point is just do not unnecessarily break the case when `WINAPI_FAMILY` is not set by undefining DESKTOP.

---

## Comment 24

> Username: jeking3  
> Created_at: 2017-10-07 23:16:54 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334971945  

If we're going to make a change again in this area, we would deprecate all of the ``BOOST_PLAT_WINDOWS_*`` definitions and move the new ones to ``BOOST_PLAT_UWP_*`` with the exception of ``BOOST_PLAT_WINDOWS_UWP`` would change to ``BOOST_PLAT_UWP``.  That will give everyone a single boost release to make all their changes for UWP, after which the legacy definitions wil be removed, and folks will be in the same boat if they didn't do anything about it.  
  
This in turn will require a change in config.hpp of Boost.WinAPI, but I think that's the extent of the changes.  
  
I won't be able to do this until next week so if someone beats me to it that's great, otherwise it'll have to wait a bit.  This sounds like the best compromise in the spirit of deprecating boostisms that I've been learning.

---

## Comment 25

> Username: pdimov  
> Created_at: 2017-10-07 23:26:16 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-334972289  

No, I backtracked on this suggestion of mine. There's no need for new macros, the existing ones were fine. Just don't change the meaning of `BOOST_PLAT_WINDOWS_DESKTOP` from the documented one:  
  
http://www.boost.org/doc/libs/1_65_1/doc/html/predef/reference.html#predef.reference.boost_plat_platform_macros  
  
There's no "again" change in this area. The change will happen in 1.66.

---

## Comment 26

> Username: Lastique  
> Created_at: 2017-10-14 20:26:24 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-336664725  

So, what will be our resolution on this ticket? I would like to have `BOOST_PLAT_WINDOWS_UWP` and other new `BOOST_PLAT_` macros in master so that I can merge Boost.WinAPI, but if we want the old default restored we should do it in develop first. @grafikrobot What is your preference?

---

## Comment 27

> Username: pdimov  
> Created_at: 2017-10-14 21:35:34 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-336669107  

Keeping the default makes the change completely backward compatible as all the existing macros retain their already documented meaning. I really see no reason to not do that even from a purely theoretical perspective. (From a practical perspective, of course, the upside is that we'll no longer need to change as much existing code - if any.)

---

## Comment 28

> Username: jeking3  
> Created_at: 2017-10-14 21:57:30 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-336670253  

So the only change then would be to have `BOOST_PLAT_WINDOWS_DESKTOP` evaluate to non-zero in a non-UWP environment.  Can we mark this behavior as deprecated, but assume we'll leave it around for long enough for other libraries to catch up?

---

## Comment 29

> Username: pdimov  
> Created_at: 2017-10-14 22:07:32 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-336670782  

I see no reason to even deprecate it. Non-UWP platforms are fine as they are, and it would do nobody any good if we break them just for the fun of it. What's the point? What's the gain?

---

## Comment 30

> Username: grafikrobot  
> Created_at: 2017-10-14 22:31:25 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-336671927  

Given the now likely fallout.. I'd prefer if we bring back the previous behavior for `BOOST_PLAT_WINDOWS_DESKTOP`. I also don't see the point in deprecating something that should be orthogonal to the UWP detection.

---

## Comment 31

> Username: jeking3  
> Created_at: 2017-10-15 02:03:08 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-336680598  

https://github.com/boostorg/predef/pull/64

---

## Comment 32

> Username: pdimov  
> Created_at: 2017-10-15 11:17:47 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-336704654  

Still insist on deprecating, I see.  
  
Your documentation changes are wrong too. The left column of the tables specify the condition under which a macro is defined. For instance, `WINAPI_FAMILY == WINAPI_FAMILY_DESKTOP_APP` means that the Predef macro is defined when `WINAPI_FAMILY == WINAPI_FAMILY_DESKTOP_APP`.  
  
You've added rows that have just `WINAPI_FAMILY_DESKTOP_APP`, `WINAPI_FAMILY_PHONE_APP` and so on. This means `#if WINAPI_FAMILY_DESKTOP_APP`, the Predef macro is defined, but that's wrong.  
  
The rows indicate independent conditions ("or"), not a conjunction. Taking an example from the docs before the change, the two rows `!WINAPI` and `WINAPI == WINAPI_FAMILY_DESKTOP_APP` did not hold together.  
  
Or at least that's my interpretation of what I'm seeing.

---

## Comment 33

> Username: jeking3  
> Created_at: 2017-10-15 12:23:06 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-336708094  

I want to fix the descriptions in the generated documentation and fix the fact that the `BOOST_PLAT_WINDOWS_UWP` section doesn't even show up.  How do I generate documentation locally?

---

## Comment 34

> Username: pdimov  
> Created_at: 2017-10-15 12:40:05 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-336708953  

`b2 doc` from `libs/predef` should work.

---

## Comment 35

> Username: pdimov  
> Created_at: 2017-10-15 12:43:14 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-336709122  

You probably need to fix the test as well, but I assume you know that already.

---

## Comment 36

> Username: jeking3  
> Created_at: 2017-10-16 03:28:47 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-336771455  

Thanks, working on it...

---

## Comment 37

> Username: jeking3  
> Created_at: 2017-10-16 03:51:35 UTC  
> Url: https://github.com/boostorg/predef/pull/57#issuecomment-336773815  

Okay should be done with CI in a couple hours on that PR.

---
