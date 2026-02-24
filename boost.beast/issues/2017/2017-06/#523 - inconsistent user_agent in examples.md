# #523 - inconsistent user_agent in examples [Closed]

> Username: octopus-prime  
> Created at: 2017-06-21 06:17:16 UTC  
> Updated at: 2017-06-22 17:41:12 UTC  
> Closed at: 2017-06-22 17:41:12 UTC  
> Url: https://github.com/boostorg/beast/issues/523  

Sometimes you use  
`  
req.set(http::field::user_agent, "Beast");  
`  
and sometimes  
`  
req.set(http::field::user_agent, BEAST_VERSION_STRING);  
`  
  
In my opinion the latter is more useful (e.g. debugging, logging).

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 11:37:24 UTC  
> Url: https://github.com/boostorg/beast/issues/523#issuecomment-310051842  

Great points, willfix
