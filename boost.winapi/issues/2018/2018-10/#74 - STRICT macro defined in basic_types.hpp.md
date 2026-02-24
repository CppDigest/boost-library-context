# #74 - STRICT macro defined in basic_types.hpp [Closed]

> Username: seanyen  
> Created at: 2018-10-12 00:28:11 UTC  
> Updated at: 2020-05-01 23:18:07 UTC  
> Closed at: 2020-05-01 23:18:07 UTC  
> Url: https://github.com/boostorg/winapi/issues/74  

In basic_types.hpp, there is a code snippet like below:  
  
https://github.com/boostorg/winapi/blob/1c890b7c1a85535d88f0b6549af7c9e6b1a80239/include/boost/winapi/basic_types.hpp#L65-L75  
  
Wondering is it necessary to define STRICT here? Or can it be rename to something else like BOOST_WINAPI_STRICT? It is nice to keep the global namespace pollution as little as possible.

---

## Comment 1

> Username: Lastique  
> Created at: 2018-10-12 10:55:27 UTC  
> Updated at: 2018-10-12 10:56:26 UTC  
> Url: https://github.com/boostorg/winapi/issues/74#issuecomment-429287091  

`STRICT` is a windows.h config macro, so it should not be used for anything other than to control Windows SDK (or Boost.WinAPI). So name clashing should not really be an issue.  
  
As to why we're defining it in Boost.WinAPI, I think, we're reflecting the Windows SDK default configuration, but I can't verify this right now. It is also important that Boost.WinAPI and Windows SDK sees the same value of the `STRICT` macro because it affects function signatures that involve handles, and those signatures must be exactly the same between Windows SDK and Boost.WinAPI. We could use our own macro but that would introduce mismatch if user's code included windows.h before or after Boost.WinAPI.
