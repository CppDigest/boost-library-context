# #55 fixed zero as null pointer error using new macro [Closed]

> Username: Shivix  
> Created at: 2020-10-20 15:29:46 UTC  
> Updated at: 2021-02-24 19:45:36 UTC  
> Closed at: 2021-02-24 19:45:27 UTC  
> Url: https://github.com/boostorg/chrono/pull/55  

Fixed the error shown in https://github.com/boostorg/chrono/issues/48  
  
This was done by creating and using a new macro in config.hpp allowing nullptr to be used if it is available without breaking C++98 compatibility.

---
