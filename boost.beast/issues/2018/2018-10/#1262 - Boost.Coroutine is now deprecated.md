# #1262 - Boost.Coroutine is now deprecated [Closed]

> Username: bcc791  
> Created at: 2018-10-06 23:51:33 UTC  
> Updated at: 2018-11-28 02:09:52 UTC  
> Closed at: 2018-11-28 02:09:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1262  

This is a question not a bug:  
  
I was trying to build the websocket co-routine example, but I get the Boost.Coroutine deprication warning. I am using Boost 1.68. Is it because `asio` still using the old  co-routine library?  
  
  
```  
 #  warning                  "Boost.Coroutine is now deprecated. Please switch to Boost.Coroutine2. To disable this warning message, define BOOST_COROUTINES_NO_DEPRECATION_WARNING."  
    ^~~~~~~  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-10-07 00:57:29 UTC  
> Url: https://github.com/boostorg/beast/issues/1262#issuecomment-427617129  

> I am using Boost 1.68. Is it because asio still using the old co-routine library?  
  
Yes. This is a sore subject, lol! That warning can be safely ignored. If the author of Boost.Coroutine removes the old coroutine library completely (which works perfectly fine and doesn't need to be fiddled with) then the author of Asio will likely just inline the relevant code into the Asio project itself.   
  
In other words, Keep Calm and Carry On.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2018-11-27 01:09:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1262#issuecomment-441859459  

This issue has been open for a while with no activity, has it been resolved?
