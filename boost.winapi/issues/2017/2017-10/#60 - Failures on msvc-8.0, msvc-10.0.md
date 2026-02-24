# #60 - Failures on msvc-8.0, msvc-10.0 [Closed]

> Username: pdimov  
> Created at: 2017-10-07 16:04:30 UTC  
> Updated at: 2017-10-08 15:10:40 UTC  
> Closed at: 2017-10-07 17:14:50 UTC  
> Url: https://github.com/boostorg/winapi/issues/60  

In the course of looking at something else, I saw that building Boost.System fails on msvc-8.0 and msvc-10.0 with:  
  
```  
compile-c-c++ ..\bin.v2\libs\system\build\msvc-10.0\debug\asynch-exceptions-on\threading-multi\error_code.obj  
error_code.cpp  
C:\Projects\boost-git\boost\boost/system/detail/local_free_on_destruction.hpp(29) : error C2039: 'LocalFree' : is not a member of 'boost::detail::winapi'  
C:\Projects\boost-git\boost\boost/system/detail/local_free_on_destruction.hpp(29) : error C3861: 'LocalFree': identifier not found  
C:\Projects\boost-git\boost\boost/system/detail/error_code.ipp(426) : error C2039: 'FORMAT_MESSAGE_ALLOCATE_BUFFER_' : is not a member of 'boost::detail::winapi  
'  
C:\Projects\boost-git\boost\boost/system/detail/error_code.ipp(426) : error C2065: 'FORMAT_MESSAGE_ALLOCATE_BUFFER_' : undeclared identifier  
```  
  
msvc-11.0 and later work.

---

## Comment 1

> Username: Lastique  
> Created at: 2017-10-07 16:25:24 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334947277  

I believe, this is caused by https://github.com/boostorg/predef/pull/57. I'm working on an update for Boost.WinAPI to accommodate that change, but due to the problem with Boost.Thread (https://github.com/boostorg/thread/pull/160) testing might be difficult.

---

## Comment 2

> Username: pdimov  
> Created at: 2017-10-07 16:28:32 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334947470  

The Thread problem should be fixed now.

---

## Comment 3

> Username: jeking3  
> Created at: 2017-10-07 16:35:48 UTC  
> Updated at: 2017-10-07 16:36:21 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334947928  

@pdimov you can work around it temporarily, locally, with the changes from https://github.com/boostorg/winapi/pull/59,  if you want to make local progress.

---

## Comment 4

> Username: Lastique  
> Created at: 2017-10-07 16:39:17 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334948134  

@jeking3 No, that fix is incomplete.

---

## Comment 5

> Username: pdimov  
> Created at: 2017-10-07 16:41:37 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334948303  

Makes no sense to me; when `BOOST_PLAT_WINDOWS_UWP` is 0 all `BOOST_WINAPI_PARTITION` are made 1? How is this correct?

---

## Comment 6

> Username: Lastique  
> Created at: 2017-10-07 16:45:29 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334948836  

The idea is that on older Windows SDKs that have no support for API families WinAPI is supposed to provide all APIs, sans the ones not available in the target Windows version. This is when `BOOST_PLAT_WINDOWS_UWP` is 0.  
  
The problem is that Boost.WinAPI only halfway ported to the partition macros and in some places directly tests Boost.Predef macros, which are all 0 after https://github.com/boostorg/predef/pull/57. The right thing to do is to port all of Boost.WinAPI to the partition macros, which should account for the older SDKs. This is what I've done locally and testing now.

---

## Comment 7

> Username: pdimov  
> Created at: 2017-10-07 16:48:51 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334949319  

But changing `BOOST_PLAT_WINDOWS_RUNTIME` from 1 to 0 on older SDKs is wrong. This change should be reverted as it has repercussions all over.

---

## Comment 8

> Username: Lastique  
> Created at: 2017-10-07 16:50:53 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334949496  

`BOOST_PLAT_WINDOWS_RUNTIME` *should* be 0 on older SDKs. The breaking change is that `BOOST_PLAT_WINDOWS_DESKTOP` was previously 1 by default on older SDKs and now is 0. The rationale is that Boost.Predef reflects the fact that the SDK does not support API families. See https://github.com/boostorg/predef/issues/54.

---

## Comment 9

> Username: pdimov  
> Created at: 2017-10-07 16:51:53 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334949568  

Right, `BOOST_PLAT_WINDOWS_DESKTOP`. :-)

---

## Comment 10

> Username: jeking3  
> Created at: 2017-10-07 17:00:50 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334950180  

@Lastique cool it'll be good to see the fixes here; I thought modifying config.hpp like I did in #59 was all that was needed.

---

## Comment 11

> Username: pdimov  
> Created at: 2017-10-07 17:05:54 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334950570  

`BOOST_PLAT_WINDOWS_DESKTOP` needs to stay 1 on older SDKs if you ask me. `#if BOOST_PLAT_WINDOWS_DESKTOP` means "Desktop APIs are available" and they are.

---

## Comment 12

> Username: Lastique  
> Created at: 2017-10-07 17:10:36 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334951012  

Both ways have its merits. I can agree with changing it back to 1 by default, although what is considered a "desktop API" is a little fuzzy since `BOOST_PLAT_WINDOWS_APP` and `BOOST_PLAT_WINDOWS_SYSTEM` families also exist on the desktop and not only there. This family stuff is a lot of mess, IMHO.

---

## Comment 13

> Username: Lastique  
> Created at: 2017-10-07 17:14:50 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334951304  

Should be fixed in https://github.com/boostorg/winapi/commit/8091170850cc0836d24d15e38de004aee35e3ee0.

---

## Comment 14

> Username: pdimov  
> Created at: 2017-10-07 17:20:37 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334951720  

The problem is that the old PLAT macros were mutually exclusive and indicated platforms, not API families. Changing them to mean (overlapping) API families is a breaking change, as code relies on their being mutually exclusive.  
  
A much more prudent approach would have been to not touch these and introduce separate FAMILY macros which new code can test.

---

## Comment 15

> Username: Lastique  
> Created at: 2017-10-07 17:28:36 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334952268  

Families are not overlapping in the sense that the user selects only one family. The Boost.Predef macros directly derive from `WINAPI_FAMILY`, which makes them indicate the family selected by the user or Windows SDK. In this light I don't see how these new FAMILY macros would differ from the existing ones.

---

## Comment 16

> Username: pdimov  
> Created at: 2017-10-07 21:09:00 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334965847  

Looking at this further, it doesn't look like the Winapi config code is quite right still, as under Win10 one can pick partitions directly.  
  
```  
/* In Windows 10, WINAPI_PARTITIONs will be used to add additional    
 * device specific APIs to a particular WINAPI_FAMILY.    
 * For example, when writing Windows Universal apps, specifying   
 * WINAPI_FAMILY_APP will hide phone APIs from compilation.    
 * However, specifying WINAPI_PARTITION_PHONE_APP=1 additionally, will           
 * unhide any API hidden behind the partition, to the compiler.  
  
 * The following partitions are currently defined:  
 * WINAPI_PARTITION_DESKTOP            // usable for Desktop Win32 apps (but not store apps)  
 * WINAPI_PARTITION_APP                // usable for Windows Universal store apps  
 * WINAPI_PARTITION_PC_APP             // specific to Desktop-only store apps  
 * WINAPI_PARTITION_PHONE_APP          // specific to Phone-only store apps  
 * WINAPI_PARTITION_SYSTEM             // specific to System applications  
  
 * The following partitions are indirect partitions and defined in   
 * winpackagefamily.h. These partitions are related to package based   
 * partitions. For example, specifying WINAPI_PARTITION_SERVER=1 will light up  
 * any API hidden behind the package based partitions that are bound to   
 * WINAPI_PARTITION_SERVER, to the compiler.  
 * WINAPI_PARTITION_SERVER             // specific to Server applications  
*/  
```

---

## Comment 17

> Username: jeking3  
> Created at: 2017-10-07 21:49:29 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334967981  

Ugh, why did they have to make this so insanely complicated?  
SDK 8.0, 8.1, and 10.0 are totally different in this respect.  
  
I'd suggest that for the 1.66 boost release we ignore this or document that we don't support it.  I think the work we've done to this point is significantly better towards supporting UWP.  We don't need to cover corner cases like this right now.

---

## Comment 18

> Username: Lastique  
> Created at: 2017-10-07 22:32:09 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-334970158  

Yep, I'm not going to support overriding or adding new partitions. Anyone willing to use this feature is free to define `BOOST_USE_WINDOWS_H`.

---

## Comment 19

> Username: pdimov  
> Created at: 2017-10-08 14:15:08 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-335009504  

Clang says  
  
```  
..\../boost/detail/winapi/config.hpp:146:7: warning: extra tokens at end of #else directive [-Wextra-tokens]  
#else if defined(WINAPI_FAMILY_DESKTOP_APP)  
      ^  
```

---

## Comment 20

> Username: Lastique  
> Created at: 2017-10-08 15:10:40 UTC  
> Url: https://github.com/boostorg/winapi/issues/60#issuecomment-335013071  

Thanks, should be fixed now.
