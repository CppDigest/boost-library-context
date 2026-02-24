# #2146 - SSL WebSocket client build error with MSVC and Boost 1.75 [Closed]

> Username: orikama  
> Created at: 2021-01-10 17:25:28 UTC  
> Updated at: 2022-01-09 05:17:08 UTC  
> Closed at: 2022-01-09 05:17:07 UTC  
> Url: https://github.com/boostorg/beast/issues/2146  

Boost: 1.75.0  
BOOST_BEAST_VERSION 301  
Visual Studio 2019 16.8.3, Microsoft (R) C/C++ Optimizing Compiler Version 19.28.29335 for x64 (I guess version doesn't really matter as long as it's MSVC)  
  
### Steps necessary to reproduce the problem  
Just build ```websocket_client_async_ssl``` example with MSVC compiler and get this:  
```  
fatal error C1128: number of sections exceeded object file format limit: compile with /bigobj  
```  
No errors using GCC or with Boost 1.74  
A small research revealed that problem was introduced in ccd0f8822ae8532ea344658092bdb985915a946b. Applying this commit to Boost 1.74 gives the same error.  
  
### Possible workarounds  
- Add ```/bigobj``` flag as compiler suggests  
- Or use ```BOOST_BEAST_SEPARATE_COMPILATION``` option and include ```boost/beast/src.hpp``` in a separate translation unit  
  
Probably opening this issue is kinda useless since it's more of an MSVC problem, but I did it just to inform you, I guess ... :)

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-01-10 17:37:36 UTC  
> Url: https://github.com/boostorg/beast/issues/2146#issuecomment-757513769  

Thanks for reporting. I'll need to do some research on this as I've never seen this error before.

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-01-10 17:39:27 UTC  
> Url: https://github.com/boostorg/beast/issues/2146#issuecomment-757514009  

Aha  
> x64 will have at least 4 sections associated with each function compiled /Gy or inlined from templates or class-inline: code, pdata, and debug info, and possibly xdata. X86 won’t have the pdata.  
  
https://docs.microsoft.com/en-us/cpp/error-messages/compiler-errors-1/fatal-error-c1128?view=msvc-160  
  
Looks like we need to update the test scripts.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2146#issuecomment-850857911  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2146#issuecomment-1008232351  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
