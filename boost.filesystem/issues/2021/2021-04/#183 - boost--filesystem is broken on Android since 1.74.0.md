# #183 - boost::filesystem is broken on Android since 1.74.0 [Closed]

> Username: andreya108  
> Created at: 2021-04-02 16:25:11 UTC  
> Updated at: 2021-04-08 10:43:09 UTC  
> Closed at: 2021-04-02 17:04:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/183  

Many operations (for example file_size) fail with an exception:  
  
Exception runtime_error caught: boost::filesystem::status: Function not implemented  
  
Can be easily reproduced.  
  
boost 1.72.0 - works fine  
boost 1.73.0 - works fine  
boost 1.74.0 - fails  
boost 1.75.0 - fails  
  
Environment details:  
Android NDK r22  
Target android api level = 23  
Target platform: aarch64

---

## Comment 1

> Username: Lastique  
> Created at: 2021-04-02 17:04:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/183#issuecomment-812619680  

I suspect this is a duplicate of https://github.com/boostorg/filesystem/issues/172. Check if the Android version you're running matches the NDK version.

---

## Comment 2

> Username: andreya108  
> Created at: 2021-04-08 10:43:09 UTC  
> Url: https://github.com/boostorg/filesystem/issues/183#issuecomment-815656754  

Definitely it matches, but the issue is real.  
Fortunately, patch from #172 applied before build helps.
