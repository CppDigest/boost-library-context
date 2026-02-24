# #143 - [lwt] `_seterrormode` is not defined for non-desktop targets [Closed]

> Username: Lastique  
> Created at: 2023-04-15 14:49:52 UTC  
> Updated at: 2023-04-16 21:50:10 UTC  
> Closed at: 2023-04-16 21:50:10 UTC  
> Url: https://github.com/boostorg/core/issues/143  

Boost.WinAPI tests are [failing](https://ci.appveyor.com/project/Lastique/winapi/builds/46782754/job/0yfotd03qxhakkvo?fullLog=true#L996) when compiled for PHONE and APP targets with MSVC 11 and 12:  
  
```  
C:\projects\boost-root\boost/core/detail/lwt_unattended.hpp(39) : error C2039: '_seterrormode' : is not a member of '`global namespace''  
C:\projects\boost-root\boost/core/detail/lwt_unattended.hpp(39) : error C3861: '_seterrormode': identifier not found  
    call "bin.v2\standalone\msvc\msvc-12.0\msvc-setup.bat" amd64 >nul  
 cl /Zm800 -nologo "libs\winapi\test\run\access_rights_abi.cpp" -c -Fo"bin.v2\libs\winapi\test\access_rights_abi.test\msvc-12.0\debug\threading-multi\run\access_rights_abi.obj"      -TP /wd4675 /EHs /GR /Z7 /Od /Ob0 /W3 /MDd /Zc:forScope /Zc:wchar_t /Zc:inline /favor:blend -DBOOST_ALL_NO_LIB=1 -DBOOST_WINAPI_DEFINE_VERSION_MACROS=1 -DWINAPI_FAMILY=WINAPI_FAMILY_PHONE_APP -D_CRT_SECURE_NO_DEPRECATE -D_CRT_SECURE_NO_WARNINGS -D_SCL_SECURE_NO_DEPRECATE -D_SCL_SECURE_NO_WARNINGS -D_WIN32_WINNT=0x0603 "-I."   
```  
  
Perhaps, this call needs to be disabled in these configs. Also, there is [`SetErrorMode`](https://learn.microsoft.com/en-us/windows/win32/api/errhandlingapi/nf-errhandlingapi-seterrormode), though I don't know if it is available in these configs.

---

## Comment 1

> Username: pdimov  
> Created at: 2023-04-15 15:08:10 UTC  
> Url: https://github.com/boostorg/core/issues/143#issuecomment-1509867317  

> Perhaps, this call needs to be disabled in these configs.  
  
What's the best way to do that?  
  
> Also, there is [SetErrorMode](https://learn.microsoft.com/en-us/windows/win32/api/errhandlingapi/nf-errhandlingapi-seterrormode), though I don't know if it is available in these configs.  
  
There is, and it's actually the right thing to call because `_seterrormode` is deprecated, but I gave up trying to declare it properly (we can't use Winapi in Core.)

---

## Comment 2

> Username: Lastique  
> Created at: 2023-04-15 15:45:05 UTC  
> Url: https://github.com/boostorg/core/issues/143#issuecomment-1509875079  

> What's the best way to do that?  
  
You could probably look at the CRT header that declares it and copy the preprocessor checks from there (or replicate the same effect). I'd look myself, but I don't have access to a Windows machine right now.  
  
> There is, and it's actually the right thing to call because `_seterrormode` is deprecated, but I gave up trying to declare it properly (we can't use Winapi in Core.)  
  
The function signature looks simple enough. No structures involved, `UINT` is `unsigned int`.

---

## Comment 3

> Username: pdimov  
> Created at: 2023-04-15 16:20:18 UTC  
> Url: https://github.com/boostorg/core/issues/143#issuecomment-1509885742  

> You could probably look at the CRT header that declares it and copy the preprocessor checks from there (or replicate the same effect).  
  
I don't think these macros stay the same across versions, although it looks like the latest Windows SDK still defines the old macros (even though they are deprecated).

---

## Comment 4

> Username: Lastique  
> Created at: 2023-04-15 16:57:22 UTC  
> Updated at: 2023-04-15 16:59:21 UTC  
> Url: https://github.com/boostorg/core/issues/143#issuecomment-1509892605  

> > You could probably look at the CRT header that declares it and copy the preprocessor checks from there (or replicate the same effect).  
>   
> I don't think these macros stay the same across versions, although it looks like the latest Windows SDK still defines the old macros (even though they are deprecated).  
  
This only needs to be done on MSVC 11 and 12. MSVC 14.3 CI for various targets passed. Though we don't test versions between 12 and 14.3 with different WinAPI families, so maybe not only 11 and 12, but the workaround is fairly contained anyway.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-04-15 17:19:24 UTC  
> Url: https://github.com/boostorg/core/issues/143#issuecomment-1509899115  

This actually depends on the Windows SDK version, not the compiler version (for 14.x). Of those I have installed, `_seterrormode` is predicated on `_CRT_USE_WINAPI_FAMILY_DESKTOP_APP` for SDK versions 10.0.10150.0 and 10.0.10240.0 (as it is for MSVC 11 and 12), and unconditionally declared for versions 10.0.17763.0 and 10.0.19041.0. I have no idea why that would be - whether `SetErrorMode` exists shouldn't be dependent on the SDK version.  
  
I think I'll omit checking the version.

---

## Comment 6

> Username: pdimov  
> Created at: 2023-04-16 12:00:03 UTC  
> Url: https://github.com/boostorg/core/issues/143#issuecomment-1510352167  

Did https://github.com/boostorg/core/commit/5904fb5636e5c8eb5265a580cd788005a014d750 fix it?

---

## Comment 7

> Username: Lastique  
> Created at: 2023-04-16 21:13:28 UTC  
> Url: https://github.com/boostorg/core/issues/143#issuecomment-1510489433  

Yes, CI has [passed](https://ci.appveyor.com/project/Lastique/winapi/builds/46799178). Thanks!
