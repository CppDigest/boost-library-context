# #2359 - How do I close a synchronous websocket server? [Closed]

> Username: johannes1971  
> Created at: 2021-12-10 10:08:54 UTC  
> Updated at: 2022-06-16 14:30:19 UTC  
> Closed at: 2022-06-16 14:30:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2359  

We have a synchronous websocket server, almost exactly the example from the website (https://www.boost.org/doc/libs/develop/libs/beast/example/websocket/server/sync/websocket_server_sync.cpp). Is there a way to close this if the _server_ decides to shut down on its own? Both the acceptor and any open sessions appear to be stuck waiting for something to happen from a remote source. Is there a way to trigger forward motion from another thread, or is that only possible in the async version?  
  
We are currently using Beast 292 from Boost 1.73.

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-12-10 12:15:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2359#issuecomment-990923599  

Short answer is no.  
Deciding to shut down the server asynchronously (i.e. while there is a read in progress) starts to argue for an asynchronous server model.

---

## Comment 2

> Username: johannes1971  
> Created at: 2021-12-10 12:25:23 UTC  
> Url: https://github.com/boostorg/beast/issues/2359#issuecomment-990930682  

I don't mind completing any on-going reads, the problem is if it's just sitting there waiting for the next read to occur. There appears to be no way to progress it at that point.   
If this means we need to switch to the asynchronous model, we'll do that. It looked a bit easier like this...

---

## Comment 3

> Username: madmongo1  
> Created at: 2021-12-11 18:43:06 UTC  
> Url: https://github.com/boostorg/beast/issues/2359#issuecomment-991744500  

Using the async model with boost.coroutine or c++20 coroutines is almost as easy as writing inline code.  
But writing async code using the continuation passing model isn't that much of a hardship once you get used to it.

---

## Comment 4

> Username: vinniefalco  
> Created at: 2021-12-12 03:00:57 UTC  
> Url: https://github.com/boostorg/beast/issues/2359#issuecomment-991825879  

> It looked a bit easier like this...  
  
Yes, well it is easier because it does less :)

---

## Comment 5

> Username: vinniefalco  
> Created at: 2022-06-16 14:30:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2359#issuecomment-1157728187  

If the issue is still unresolved please reopen it or create a new issue, thanks!
