# #684 - Broken cygwin b2 start [Closed]

> Username: viric  
> Created at: 2020-12-15 23:04:40 UTC  
> Updated at: 2020-12-17 18:19:24 UTC  
> Closed at: 2020-12-17 17:59:15 UTC  
> Url: https://github.com/boostorg/build/issues/684  

Cygwin makes executable_path() go under the fallback path:  
https://github.com/boostorg/build/blob/develop/src/engine/jam.cpp#L780  
  
If the user calls b2 with a relative path (./b2) it will return NULL string and make the program terminate by uncaught logical_error exception, when it tries to construct a std::string from that executable_path():  
https://github.com/boostorg/build/blob/develop/src/engine/startup.cpp#L159-L160  
  
A workaround for a user is to call b2 with a full path:  $PWD/b2   although it is not evident as there is no message about that.  
  
A fix would be to use the same executable_path() implementation as linux, as /proc/self/exe is the same kind of symlink. And maybe startup.cpp should handle the NULL result better.

---

## Comment 1

> Username: grafikrobot  
> Created at: 2020-12-17 17:59:15 UTC  
> Url: https://github.com/boostorg/build/issues/684#issuecomment-747601578  

https://github.com/boostorg/build/pull/686  
https://github.com/boostorg/build/commit/979f66b69d199f859e0f479ee82ca7f5ac2418e9

---

## Comment 2

> Username: viric  
> Created at: 2020-12-17 18:19:23 UTC  
> Url: https://github.com/boostorg/build/issues/684#issuecomment-747612607  

develop was already fixed. I didn't notice.
