# #2133 - async_read() or async_write() [Closed]

> Username: mirkub  
> Created at: 2020-12-03 08:47:58 UTC  
> Updated at: 2022-01-09 05:17:03 UTC  
> Closed at: 2022-01-09 05:17:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2133  

Hi,  
I would have a general question as how to implement a http client which sends an initial request to a server and then 2 events may happen:  
1) User wants to send another HTTP request (in pipeline)  
or  
2) Response comes from the server and client should read data from the stream.  
  
So it's not clear whether on on_read() callback do queue.pop() which blocks until new request from user arrives or async_read() which waits until response from server comes. Any hits are appreciated.

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-12-03 09:14:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2133#issuecomment-737771270  

I need to know more about your client interface in order to answer fully. It sounds as if you are writing a REST client, and you want clients of the REST client to be able to submit requests in arbitrary order and at any time, with a guarantee that the requests will be treated in the correct order.  
In effect, it's an RPC client?

---

## Comment 2

> Username: mirkub  
> Created at: 2020-12-03 09:23:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2133#issuecomment-737776291  

Yes, exactly, I'd like to have a rest client which can send multiple requests without waiting for responses.  
Perhaps the on_write() callback can do both: http::async_read() and http::async_write() if there are new requests in the queue?

---

## Comment 3

> Username: madmongo1  
> Created at: 2020-12-03 09:26:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2133#issuecomment-737777705  

yes, that would work. Be careful. on_write() should not launch another async_read if there is already a read in progress. It should queue the read operation, which can be dequed and initiated by in the async_read's handler.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:18 UTC  
> Url: https://github.com/boostorg/beast/issues/2133#issuecomment-850857946  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:37 UTC  
> Url: https://github.com/boostorg/beast/issues/2133#issuecomment-1008232332  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
