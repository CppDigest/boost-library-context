# #3 [winrt support] Adding macro to help check and test what API of the Windows Runtime is being used. [Closed]

> Username: stgates  
> Created at: 2014-05-13 23:44:48 UTC  
> Updated at: 2014-05-23 00:03:21 UTC  
> Closed at: 2014-05-22 23:52:47 UTC  
> Url: https://github.com/boostorg/predef/pull/3  

This change adds a macro BOOST_WINDOWS_RUNTIME that can be used by individual Boost libraries to determine which Windows APIs are available for use.  
  
This is part of the work mentioned on the Boost dev mailing list to help get portions of Boost working in Windows store and phone.  
  
http://boost.2283326.n4.nabble.com/winrt-support-Adding-support-for-Windows-8-store-phone-to-Boost-libraries-tc4661713.html  
  
Originally I added this macro to Boost.Config but received feedback on the name and that it better belongs in Boost.Predef. For history here is the previous pull request from Boost.Config:  
https://github.com/boostorg/config/pull/13

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2014-05-22 15:27:09 UTC  
> Url: https://github.com/boostorg/predef/pull/3#discussion_r12955954  

Since copyrights do expire this needs to include a year indicating the age of the changes.

---

## Comment 2

> Username: grafikrobot  
> Created_at: 2014-05-22 15:32:04 UTC  
> Url: https://github.com/boostorg/predef/pull/3#discussion_r12956233  

According to the [VC docs](http://msdn.microsoft.com/en-us/library/gg155713%28v=winembedded.70%29.aspx) the _M_ARM macro defines the major version of the ARM architecture. I would prefer if that version is defined like the other predefs as V.0.0 for completeness and consistency.

---

## Comment 3

> Username: grafikrobot  
> Created_at: 2014-05-22 15:33:27 UTC  
> Url: https://github.com/boostorg/predef/pull/3#discussion_r12956330  

Needs copyright year.

---

## Comment 4

> Username: grafikrobot  
> Created_at: 2014-05-22 16:15:19 UTC  
> Url: https://github.com/boostorg/predef/pull/3#discussion_r12958874  

After a week of thinking about this change, trying to figure out from online docs what exactly the whole FAMILY thing is and not getting anywhere, here's what I suggest...  
  
First, basics:  
1. _ALL_ the definitions in Boost Predef must be defined in terms of a version number. Just defining a macro is not enough. In places where the choice is binary detection you can define it as either BOOST_VERSION_NUMBER_AVAILABLE or BOOST_VERSION_NUMBER_NOT_AVAILABLE.  
2. _ALL_ possible macros in Predef must be defined. In your change BOOST_WINDOWS_RUNTIME is not always defined. And for each defined macro there's an additional name_AVAILABLE.  
  
Second, specifics:  
1. BOOST_NO_ANSI_APIS does not belong in Predef. Predef intentionally stays away from "feature" detection. Instead it concentrates on version/availability detecion. It sounds to me that ANSI APIS is not specific enough. What does it mean to not have ANSI APIs? Which APIs specifically?  
  
Third, goal:  
1. The basic FAMILY aspect/macro needs to be it's own concept, implemented as a separate predef file(s). Two options are possible depending on what FAMILY actually means (I don't actually know even after reading the MS docs)..  
  
A. Create a set of sub BOOST_OS_WINDOWS predefs with appropriate exclusion and detection. See the OS_BSD and sub-OS macros for an example.  
  
B. Create new platform definitions for this. Perhaps BOOST_PLAT_WINDOWS_DESKTOP, BOOST_PLAT_WINDOWS_PHONE, etc. All defined to an appropriate version if possible. See BOOST_PLAT_MINGW and an example. Note, the WINAPI_FAMILY_\* macros would need to get translated to a Predef style version number.  
  
I leave it up to you as to which option better reflects the intent of the detection which of A or B is best.  
  
And when doing all this please separate out this change as it's own pull request. And put the _M_ARM change as a seperate pull (as I can merge that one as soon as my comments are addressed).

---

## Comment 5

> Username: stgates  
> Created_at: 2014-05-22 18:18:51 UTC  
> Url: https://github.com/boostorg/predef/pull/3#discussion_r12965498  

Agreed will fix.

---
