# #58 WinCE support corrections (fixes trac 5431) [Closed]

> Username: jeking3  
> Created at: 2017-09-22 01:04:19 UTC  
> Updated at: 2017-09-23 18:31:11 UTC  
> Closed at: 2017-09-23 18:31:11 UTC  
> Url: https://github.com/boostorg/winapi/pull/58  

Also added 3 CI jobs to verify.  They are at the beginning, but can be moved later.  
  
CI Travis on this PR: https://travis-ci.org/jeking3/winapi/builds/278411351  
CI Appveyor on this PR: https://ci.appveyor.com/project/jeking3/winapi/build/1.0.29-develop

---

## Comment 1

> Username: Lastique  
> Created_at: 2017-09-22 01:52:01 UTC  
> Updated_at: 2017-09-22 01:52:31 UTC  
> Url: https://github.com/boostorg/winapi/pull/58#issuecomment-331328647  

I'm not quite comfortable with this change. It looks like there are multiple different versions of the Interlocked functions declarations in different SDKs, so the change likely fixes one compiler and breaks another. I don't have a WinCE testing environment and I don't believe simply defining `_WIN32_WCE` does the proper testing. One has to setup the proper Windows CE cross-compiler (different versions of the compiler, actually) and run the tests against it.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-09-22 01:57:39 UTC  
> Url: https://github.com/boostorg/winapi/pull/58#issuecomment-331329387  

On Sep 21, 2017 9:52 PM, "Andrey Semashev" <notifications@github.com> wrote:  
  
I'm not quite comfortable with this change. It looks like there are  
multiple different versions of the Interlocked functions declarations in  
different SDKs, so the change is fixes one compiler and breaks another. I  
don't have a WinCE testing environment and I don't believe simply defining  
_WIN32_WCE does the proper testing. One has to setup the proper Windows CE  
cross-compiler (different versions of the compiler, actually) and run the  
tests against it.  
  
—  
You are receiving this because you authored the thread.  
Reply to this email directly, view it on GitHub  
<https://github.com/boostorg/winapi/pull/58#issuecomment-331328647>, or mute  
the thread  
<https://github.com/notifications/unsubscribe-auth/ALOdbYJdlBq99fuulMIB_S1Q93N0ArUCks5skxLBgaJpZM4PgGgB>  
.  
  
It sounds like neither of us has the environment set up to do this.  Plus  
is WindowsCE still active and used any more?

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-09-22 03:54:47 UTC  
> Url: https://github.com/boostorg/winapi/pull/58#issuecomment-331343549  

Well, Windows CE 6.0 is still "active" but will be EOL soon:  
June 10, 2018 - Windows Embedded CE 6.0 will be End of Life  
  
I did follow the suggestion from someone in trac 5431 to resolve the build issue though.

---

## Comment 4

> Username: Lastique  
> Created_at: 2017-09-22 08:27:20 UTC  
> Url: https://github.com/boostorg/winapi/pull/58#issuecomment-331384837  

@pdimov Do you have any background for the code in interlocked.hpp related to Windows CE? In particular, was that code to be removed by this PR tested and required with some compiler and/or SDK version?

---

## Comment 5

> Username: Lastique  
> Created_at: 2017-09-22 08:28:27 UTC  
> Url: https://github.com/boostorg/winapi/pull/58#issuecomment-331385063  

@jeking3 Am I right that the change to time.hpp is a no-op?

---

## Comment 6

> Username: pdimov  
> Created_at: 2017-09-22 10:58:14 UTC  
> Url: https://github.com/boostorg/winapi/pull/58#issuecomment-331416878  

I've never tested under CE, the changes have been contributed by others. But just defining _WIN32_WCE does not a CE make. Windows CE is (was) a separate Windows version, with its own SDK.

---

## Comment 7

> Username: jeking3  
> Created_at: 2017-09-22 14:03:22 UTC  
> Url: https://github.com/boostorg/winapi/pull/58#issuecomment-331455828  

@lastique Without the change in time.hpp, Visual Studio 2013 had compiler errors, however moving the "force inline" methods above the WCE method that calls them made them defined and available, and fixed the issue.  
  
I don't have any WCE experience; was just trying to clean up the backlog.

---

## Comment 8

> Username: Lastique  
> Created_at: 2017-09-23 18:31:11 UTC  
> Url: https://github.com/boostorg/winapi/pull/58#issuecomment-331661242  

I've committed fixes do develop.

---
