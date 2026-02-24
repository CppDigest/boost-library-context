# #24 - seed_rng.hpp does not compile for Windows UWP apps [Closed]

> Username: strentler  
> Created at: 2017-09-04 08:39:19 UTC  
> Updated at: 2017-12-18 14:59:30 UTC  
> Closed at: 2017-12-18 14:59:30 UTC  
> Url: https://github.com/boostorg/uuid/issues/24  

These Win32 functions are not allowed to be used with UWP apps:  
  
CryptAcquireContext  
CryptReleaseContext  
CryptGenRandom  
  
see here  
https://msdn.microsoft.com/en-us/library/windows/desktop/aa379886(v=vs.85).aspx  
  
Some replacement functions should be used instead.  
  
I have tried to compile using this Windows SDK/ Target Platform Version: 10.0.15063.0

---

## Comment 1

> Username: jeking3  
> Created at: 2017-09-04 13:32:44 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-326964417  

This sounds similar to the following:  
  
https://svn.boost.org/trac10/ticket/11841  
  
Could you try the workaround provided there and see if it works for you?  
I'm considering removing all the built-in random in boost UUID, and requiring the use of boost::random to initialize random behavior.

---

## Comment 2

> Username: eldiener  
> Created at: 2017-09-04 13:52:53 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-326968804  

Removing built-in random, if you do, would be a breaking change, so please be aware of the usual cycle of deprecating at least one release first before eventually changing.

---

## Comment 3

> Username: jeking3  
> Created at: 2017-09-04 16:31:07 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-326998706  

Sounds good, I'll have to look at what it is doing and make sure it is okay to deprecate it.  In the meantime, hopefully the workaround provided helps @strentler.

---

## Comment 4

> Username: jeking3  
> Created at: 2017-09-04 17:14:13 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-327004215  

@eldiener perhaps the default constructor could internally switch to using something from boost::random which would allow the internal random/crypt code to be removed without breaking the external interface.  I'll take a look...

---

## Comment 5

> Username: jeking3  
> Created at: 2017-09-04 18:43:56 UTC  
> Updated at: 2017-09-04 18:54:43 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-327014026  

@eldiener the public documentation for basic_random_generator makes no guarantees or mention of using the built-in seed mechanism.  However it looks like random generators in boost::random are also under no guarantee to make themselves random on every execution; in fact the documentation for mersenne_twister says if you use the default seed, the same value sequence will be produced for every instance of the application.  I didn't know this!

---

## Comment 6

> Username: swatanabe  
> Created at: 2017-09-04 19:23:33 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-327017969  

AMDG  
  
  A default constructed mt19937 will always produce the  
same sequence, which isn't desirable for uuid.  
  
In Christ,  
Steven Watanabe

---

## Comment 7

> Username: jeking3  
> Created at: 2017-09-04 23:27:22 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-327038719  

Thanks, I submitted a pull request today that should address this by  
leveraging boost::random::random_device to provide the entropy in a more  
platform-friendly way.  
  
On Mon, Sep 4, 2017 at 3:23 PM, swatanabe <notifications@github.com> wrote:  
  
> AMDG  
>  
> A default constructed mt19937 will always produce the  
> same sequence, which isn't desirable for uuid.  
>  
> In Christ,  
> Steven Watanabe  
>  
> —  
> You are receiving this because you were assigned.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/uuid/issues/24#issuecomment-327017969>, or mute  
> the thread  
> <https://github.com/notifications/unsubscribe-auth/ALOdbS2RjhA-4R67CrmitQQXzc9bYIfQks5sfE42gaJpZM4PLncp>  
> .  
>

---

## Comment 8

> Username: jeking3  
> Created at: 2017-09-05 14:33:26 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-327194693  

Anyone know how to "connect" this issue to the PR #25?

---

## Comment 9

> Username: Lastique  
> Created at: 2017-09-07 18:27:20 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-327884431  

I think we could use BCrypt API on UWP targets: [BCryptOpenAlgorithmProvider](https://msdn.microsoft.com/en-us/library/windows/desktop/aa375479(v=vs.85).aspx), [BCryptCloseAlgorithmProvider](https://msdn.microsoft.com/en-us/library/windows/desktop/aa375377(v=vs.85).aspx), [BCryptGenRandom](https://msdn.microsoft.com/en-us/library/windows/desktop/aa375458(v=vs.85).aspx). We could potentially use it on desktop as well, but I'm not quite sure since what Windows version this API is supported (the MSDN articles cannot be trusted on this).

---

## Comment 10

> Username: Lastique  
> Created at: 2017-09-07 18:30:06 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-327885166  

Also, it needs to be checked if this API is supported in MinGW.

---

## Comment 11

> Username: MarcelRaad  
> Created at: 2017-09-07 19:13:23 UTC  
> Updated at: 2017-09-07 19:13:54 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-327896275  

Yes, CNG / bcrypt works fine on UWP as well as the older Windows store apps. It's supported on Windows Vista and above. The application doesn't start because of missing bcrypt.dll when using it on Windows XP. Original MinGW from mingw.org doesn't have bcrypt.h [1], but MinGW-w64 does [2].  
  
[1] https://sourceforge.net/p/mingw/mingw-org-wsl/ci/5.0-active/tree/w32api/include/  
[2] https://sourceforge.net/p/mingw-w64/mingw-w64/ci/master/tree/mingw-w64-headers/include/bcrypt.h

---

## Comment 12

> Username: Lastique  
> Created at: 2017-09-07 19:19:25 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-327897659  

Thank you for the information. It looks like we'll have to keep the CryptoAPI version for the cases where BCrypt is not available.

---

## Comment 13

> Username: jeking3  
> Created at: 2017-09-08 20:55:52 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328211735  

In order to test this, apart from using the latest Platform SDK (does it come with Visual Studio 2017, if so we will shortly have an Appveyor job for that) is there anything else I need to do in order to build for UWP?  I believe the Appveyor job for 2017 is working, but I didn't make any changes; do I need to modify the WINVER or _WIN32_WINNT variables, or something a little newer?

---

## Comment 14

> Username: strentler  
> Created at: 2017-09-09 09:23:36 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328265633  

Just install the most current VS2017.  
The 15086 SDK should be installed by default.  
In VS 2017 just create a "Blank" UWP C++ App.  
Then try to use UUID. The compile should complain.

---

## Comment 15

> Username: jeking3  
> Created at: 2017-09-09 13:52:50 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328278637  

I'm hoping to find a way to do a build of boostorg/uuid in appveyor and trigger the failure so that verification of the environment is ongoing in future builds.  Any thoughts?

---

## Comment 16

> Username: MarcelRaad  
> Created at: 2017-09-09 22:48:40 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328308019  

@jeking3 I don't know if this helps for what you're trying to do, but the failure can be reproduced by running the tests with:  
```  
..\..\..\b2.exe toolset=msvc-14.1 define="WINAPI_FAMILY=WINAPI_FAMILY_APP" define="BOOST_USE_WINDOWS_H"  
```

---

## Comment 17

> Username: jeking3  
> Created at: 2017-09-09 23:49:42 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328310335  

That's perfect - thanks!

---

## Comment 18

> Username: jeking3  
> Created at: 2017-09-10 00:11:33 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328311109  

Interesting, I installed the latest platform sdk (windows kits) and I closed and opened a new shell, and it's still referencing the old one for some reason, but it is failing with the errors you said, and also:  
```  
C:\Program Files (x86)\Windows Kits\10\include\10.0.14393.0\um\combaseapi.h(1189): error C3861: 'CoCreateInstanceFromApp': identifier not found  
C:\Program Files (x86)\Windows Kits\10\include\10.0.14393.0\um\combaseapi.h(1210): error C3861: 'CoCreateInstanceFromApp': identifier not found  
```  
  
however I have this in the environment:  
```  
WindowsSDKLibVersion=10.0.15063.0\  
WindowsSdkVerBinPath=C:\Program Files (x86)\Windows Kits\10\bin\10.0.15063.0\  
WindowsSDKVersion=10.0.15063.0\  
```  
  
Strange... I'll dig into it.

---

## Comment 19

> Username: jeking3  
> Created at: 2017-09-10 03:05:47 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328316521  

So I have a decent Appveyor build environment now (see: https://github.com/jeking3/uuid/pull/1) and I can re-enable mingw, however uuid depends on serialization, and serialization has a number of issues; first of all the appveyor in that project is broken; second there are multiple issues building on Windows and on MinGW with the develop branch.  Unfortunately I'm going to have to work on fixing those before I can prove uuid builds clean on all these platforms.

---

## Comment 20

> Username: strentler  
> Created at: 2017-09-10 07:40:10 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328325630  

You get the same error for 10.0.14393.0 because it is already an older issue.

---

## Comment 21

> Username: MarcelRaad  
> Created at: 2017-09-10 11:35:44 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328336966  

As to why the older SDK version is used, you might want to remove the `b2_msvc_*.cmd` files Boost automatically creates in `%TEMP%`. It contains the INCLUDE directories used when you first ran b2, which might not even exist anymore. That breaks compilation for me with almost every Visual Studio update :-(

---

## Comment 22

> Username: jeking3  
> Created at: 2017-09-10 12:54:04 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328340857  

Interesting, I would have expected a b2 clean to remove those as well.  Perhaps they are made during bootstrap?

---

## Comment 23

> Username: jeking3  
> Created at: 2017-09-10 15:29:32 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328350075  

Given the challenges in the serialization library develop branch (won't build on linux or mingw right now) I am disabling the serialization unit test in uuid (it's been stable for a long time) until serialization can fix build issues.  This will allow me to proceed with this issue and prove it out in CI.

---

## Comment 24

> Username: jeking3  
> Created at: 2017-09-10 20:34:27 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328370022  

Even after doing that I'm getting:  
```  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\combaseapi.h(1193): error C3861: 'CoCreateInstanceFromApp': identifier not found  
C:\Program Files (x86)\Windows Kits\10\include\10.0.15063.0\um\combaseapi.h(1214): error C3861: 'CoCreateInstanceFromApp': identifier not found  
c:\boost\boost/detail/winapi/crypt.hpp(152): error C2039: 'CryptAcquireContextW': is not a member of '`global namespace''  
c:\boost\boost/detail/winapi/crypt.hpp(152): error C2873: 'CryptAcquireContextW': symbol cannot be used in a using-declaration  
c:\boost\boost/detail/winapi/crypt.hpp(153): error C2039: 'CryptGenRandom': is not a member of '`global namespace''  
c:\boost\boost/detail/winapi/crypt.hpp(153): error C2873: 'CryptGenRandom': symbol cannot be used in a using-declaration  
c:\boost\boost/detail/winapi/crypt.hpp(197): error C2039: 'CryptEnumProvidersW': is not a member of '`global namespace''  
c:\boost\boost/detail/winapi/crypt.hpp(208): error C2039: 'CryptEnumProvidersW': is not a member of '`global namespace''  
c:\boost\boost/detail/winapi/crypt.hpp(218): error C2039: 'CryptAcquireContextW': is not a member of '`global namespace''  
c:\boost\boost/detail/winapi/crypt.hpp(218): error C3861: 'CryptAcquireContextW': identifier not found  
c:\boost\boost/uuid/seed_rng.hpp(99): error C2039: 'CryptAcquireContextW': is not a member of 'boost::detail::winapi'  
c:\boost\boost/detail/winapi/get_current_thread_id.hpp(28): note: see declaration of 'boost::detail::winapi'  
c:\boost\boost/uuid/seed_rng.hpp(99): error C3861: 'CryptAcquireContextW': identifier not found  
c:\boost\boost/uuid/seed_rng.hpp(167): error C2039: 'CryptGenRandom': is not a member of 'boost::detail::winapi'  
c:\boost\boost/detail/winapi/get_current_thread_id.hpp(28): note: see declaration of 'boost::detail::winapi'  
c:\boost\boost/uuid/seed_rng.hpp(167): error C3861: 'CryptGenRandom': identifier not found  
```  
  
It's very odd to get an error like that from the windows headers themselves!

---

## Comment 25

> Username: mclow  
> Created at: 2017-09-10 23:33:46 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328380581  

That suggests (to me) that somewhere there's a macro definition that's replacing something that you don't expect.  
  
Try looking at the preprocessed source to see if you can figure out where things went wonky

---

## Comment 26

> Username: MarcelRaad  
> Created at: 2017-09-11 07:31:02 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328442460  

The declaration of `CoCreateInstanceFromApp` is only a few lines further up in this file. It's guarded by `_WIN32_WINNT >= 0x0602` (Windows 8). Maybe you're setting `_WIN32_WINNT` explicitly somehow? This Windows SDK defaults to `0x0A00` (Windows 10), which should be fine.

---

## Comment 27

> Username: Lastique  
> Created at: 2017-09-11 09:39:44 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328476597  

I think the problem is that `CoCreateInstanceFromApp` is referenced at all. I didn't find any references to it from Boost, so it must be the Windows SDK itself. Barring any contrived macro-related problems, this might be a bug in the Windows SDK. In that case it should be reported to Microsoft.

---

## Comment 28

> Username: jeking3  
> Created at: 2017-09-11 17:31:04 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328600981  

I wonder if we can just use BCRYPT_USE_SYSTEM_PREFERRED_RNG and not have to deal with opening or closing an algorithm provider at all...

---

## Comment 29

> Username: jeking3  
> Created at: 2017-09-12 13:06:52 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-328847344  

Looks like boostorg/random needs to be fixed as well; I've proposed on the boost mailing list that this seed_rng implementation be fixed and then moved into boost::random, replacing the random_device there, and at the same time enabling boost::random to become a header-only library.  seed_rng provides more entropy and with the bcrypt fixes will support more platforms, and uuid has proven it works as a header-only library.

---

## Comment 30

> Username: jeking3  
> Created at: 2017-09-15 23:53:40 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-329928793  

This has turned into a fairly complex issue involving winapi, random, and uuid.  I am workin on the winapi part now.  The first step was to get a proper CI environment into winapi so that a future pull request for bcrypt would be properly tested on many targets.  winapi actually has no support for UWP yet so that's another thing I will be looking into as well.

---

## Comment 31

> Username: jeking3  
> Created at: 2017-09-20 20:13:15 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-330967206  

As I'm planning to move this functionality into boost::random and make that software-only, I want to get folks take on the way I'm doing it:  
  
https://github.com/boostorg/random/compare/develop...jeking3:header-only-random-device  
  
These changes depend on the work I did in winapi to support UWP:  
  
https://github.com/boostorg/winapi/pull/57  
  
Given boost::random_device becomes header-only, uuids::detail::seed_rng can be removed in favor of using boost::random_device to satisfy entropy with those changes.  I'm primarily looking for comments on the strategy I took to maintain API compatibility.  All of the original APIs behave the same as they did before, however I added bcrypt support for Windows SDK 10.0 and later (SDK 8.x defines bcrypt as DESKTOP only), and I was able to build random with WINAPI_FAMILY_DESKTOP_APP (in which case wincrypt is used) and WINAPI_FAMILY_PC_APP (in which case bcrypt is used).  
  
I was unable to use rand_s as the source code for it shows that it relies on RtlGenRandom which is not available on all family partitions.

---

## Comment 32

> Username: jeking3  
> Created at: 2017-10-18 19:42:09 UTC  
> Url: https://github.com/boostorg/uuid/issues/24#issuecomment-337705635  

Still hoping to get this resolved for version 1.66.0, however waiting for @swatanabe to move boostorg/random#29 forward.
