# #2358 - Problem with Beast and Samsung Internet (websockets, unencrypted) [Closed]

> Username: johannes1971  
> Created at: 2021-12-10 10:02:46 UTC  
> Updated at: 2022-06-16 14:31:43 UTC  
> Closed at: 2022-06-16 14:29:42 UTC  
> Url: https://github.com/boostorg/beast/issues/2358  

I'm using Beast (from Boost 1.73; Beast 292), in synchronous mode, as a server for websockets (without encryption, just ws://), on Windows. The client application (written in Javascript) can communicate just fine from everything we've tried (Safari, Chrome, Firefox, Edge, etc.), with the exception of "Samsung Internet", apparently the default browser on some Android phones, where Beast complains about bad_method before aborting the connection. While not a major deal (we can tell our users to just use Chrome or Firefox instead), I was still wondering if this is a known problem in either Samsung Internet or Beast, and if there is something we can do to make it work.  
  
Our code is an only mildly adjusted version of the Beast synchronous websocket server example.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-12-10 12:13:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2358#issuecomment-990922639  

Are you able to post an example of a request header that Beast rejects?  
Beast aims to be strictly standards compliant. If the client is sending a malformed request, we may be less forgiving than other libraries.  
However, it is possible that we have an error. I'd like to check.

---

## Comment 2

> Username: johannes1971  
> Created at: 2021-12-10 12:26:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2358#issuecomment-990931313  

I can try. What's the best way to obtain such a header?

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-12-11 18:41:24 UTC  
> Url: https://github.com/boostorg/beast/issues/2358#issuecomment-991743916  

```  
log_stream << received_request;  
```

---

## Comment 4

> Username: johannes1971  
> Created at: 2021-12-15 13:59:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2358#issuecomment-994817138  

> ```  
> log_stream << received_request;  
> ```  
  
What is received_request in this context? I'm guessing it must be something related to the acceptor?

---

## Comment 5

> Username: madmongo1  
> Created at: 2021-12-15 14:48:11 UTC  
> Url: https://github.com/boostorg/beast/issues/2358#issuecomment-994862168  

> > ```  
> > log_stream << received_request;  
> > ```  
>   
> What is received_request in this context? I'm guessing it must be something related to the acceptor?  
  
It's the request http request you received:  
  
https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/beast/ref/boost__beast__http__request.html  
  
https://www.boost.org/doc/libs/1_78_0/libs/beast/doc/html/beast/ref/boost__beast__websocket__stream/async_accept/overload3.html

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-06-16 14:29:41 UTC  
> Url: https://github.com/boostorg/beast/issues/2358#issuecomment-1157727549  

is this resolved?

---

## Comment 7

> Username: johannes1971  
> Created at: 2022-06-16 14:31:43 UTC  
> Url: https://github.com/boostorg/beast/issues/2358#issuecomment-1157729698  

No, but I left that job, and no longer have access to the project. So please feel free to close.
