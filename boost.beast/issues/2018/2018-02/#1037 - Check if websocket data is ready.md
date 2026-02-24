# #1037 - Check if websocket data is ready [Closed]

> Username: thomasj02  
> Created at: 2018-02-22 08:03:29 UTC  
> Updated at: 2018-02-22 19:15:11 UTC  
> Closed at: 2018-02-22 19:15:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1037  

The synchronous `websocket.read()` seems to block indefinitely until data arrives. Is there a way to see if there is data available to be read before actually calling `read()`?

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-02-22 14:00:39 UTC  
> Url: https://github.com/boostorg/beast/issues/1037#issuecomment-367689167  

No. See: https://github.com/boostorg/beast/issues/1035

---

## Comment 2

> Username: thomasj02  
> Created at: 2018-02-22 19:15:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1037#issuecomment-367789062  

OK, thank you for the response
