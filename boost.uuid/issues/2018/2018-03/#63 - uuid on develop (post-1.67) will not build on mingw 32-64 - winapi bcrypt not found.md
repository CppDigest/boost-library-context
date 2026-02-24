# #63 - uuid on develop (post-1.67) will not build on mingw 32/64 - winapi bcrypt not found [Closed]

> Username: jeking3  
> Created at: 2018-03-26 17:19:30 UTC  
> Updated at: 2018-03-26 20:04:13 UTC  
> Closed at: 2018-03-26 20:04:13 UTC  
> Url: https://github.com/boostorg/uuid/issues/63  

cc: @Lastique   
  
Something changed in winapi - not sure what yet.  CI build system caught it on my latest PR:  
  
https://ci.appveyor.com/project/jeking3/uuid-gaamf/build/1.0.102-develop/job/6bv0e4ca312n7dyu#L1187  
  
Verified the issue locally, but haven't investigated (yet)...

---

## Comment 1

> Username: Lastique  
> Created at: 2018-03-26 18:31:26 UTC  
> Url: https://github.com/boostorg/uuid/issues/63#issuecomment-376266463  

BCrypt is only enabled for Windows Vista and later. Boost.WinAPI used to enable BCrypt on MinGW-w64 unconditionally, this has been fixed in https://github.com/boostorg/winapi/commit/97adb533ae27bfb6c9d738f5948b63a9c6ba9ea5. I suspect, in your case you're building for pre-Vista.
