# #465 Android: fix compile break with musl [Open]

> Username: ChristophStrehle  
> Created at: 2026-01-14 10:12:44 UTC  
> Updated at: 2026-01-14 10:17:11 UTC  
> Url: https://github.com/boostorg/asio/pull/465  

Fix a compile break when building with _LIBCPP_HAS_MUSL_LIBC and an Android API older then 28  
  
On Android both `__linux__` and `__ANDROID__` are defined. First check for `__ANDROID__` to get the correct behavior and not run into the Linux case  
  
Compile break was introduced with  
8b80ad7f2447cdbfc99f02a89a75d424dc8f9910  
  
This commit is missing in the upstream repository at https://github.com/chriskohlhoff/asio. Therefore I opened this PR here.

---
