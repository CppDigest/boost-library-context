# #2152 - MSVC warning about unused variable [Closed]

> Username: oxygene  
> Created at: 2021-01-25 07:45:00 UTC  
> Updated at: 2024-06-06 05:34:22 UTC  
> Closed at: 2022-09-24 05:04:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2152  

I'm getting a warning from MSVC about `unreferenced formal parameter` in boost 1.75.0 here:  
  
https://github.com/boostorg/beast/blob/855fc23885307aeb6ad1318001a4bbdd4345d47c/include/boost/beast/websocket/impl/ssl.hpp#L65

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-01-25 07:47:45 UTC  
> Url: https://github.com/boostorg/beast/issues/2152#issuecomment-766619023  

Great, thanks for letting me know.

---

## Comment 2

> Username: KnoerleMan  
> Created at: 2021-02-21 11:55:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2152#issuecomment-782845687  

Was not able to reproduce this warning with the following setup and using /Wall on boost 1.75.0 (x64):  
  
Visual Studio 16 2019  
Windows SDK version 10.0.18362.0 to target Windows 10.0.19042  
MSVC 19.28.29335.0  
  
@oxygene could you provide further information about your setup?

---

## Comment 3

> Username: oxygene  
> Created at: 2021-02-22 07:18:25 UTC  
> Url: https://github.com/boostorg/beast/issues/2152#issuecomment-783149378  

That's interesting, as `/Wall` is supposed to `Displays all warnings displayed by /W4 and all other warnings that /W4 doesn't include—for example, warnings that are off by default.` as per documentation.  
  
We are using MSVC2017 with default `/W3` warning level, but selectively pull in other warnings like `/w34100` (https://docs.microsoft.com/en-us/cpp/error-messages/compiler-warnings/compiler-warning-level-4-c4100) which is the one I mentioned in this issue.  
  
The code line mentioned in my first post undoubtedly declares `bytes_transferred` but does not use it anywhere. Unfortunately, I don't understand the code base good enough to tell whether the second parameter is needed to satisfy some concept or interface, but I've seen `boost::ignore_unused(bytes_transferred)` being used in other places and maybe this is the solution here as well?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-02-22 17:01:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2152#issuecomment-783520922  

just leave out the parameter name if it is unused

---

## Comment 5

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:04:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2152#issuecomment-1256862004  

Closing this as the fix seems to be merged.
