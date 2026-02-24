# #1802 - [Question] Ordering of Asio tasks execution [Closed]

> Username: blackBeaRR  
> Created at: 2020-01-16 13:39:29 UTC  
> Updated at: 2020-02-22 17:40:54 UTC  
> Closed at: 2020-02-22 17:40:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1802  

Hello everyone!  
  
I'd like to implement an asynchronous HTTP client with `Keep-Alive` feature using `Boost::Beast`.  
My idea is to create Client object, resolve and connect to the host, and then send get/post/delete requests asynchronously (`io_context` will be working because of `io_context::work` object). But I have a question, about how does it works on lower layer.  
  
Say, I have two requests:  
`Req1` - time to handle on the server is 10 seconds;  
`Req2` - time to handle on the server is 5 seconds;  
  
and I'm doing two requests immediately:  
```  
client->Get(Req1, ...);  
client->Get(Req2, ...);  
```  
  
Note that I suppose to have one connected socket into the client object and use `Keep-Alive` mechanism, and I have the one certain thread running `io_context::run()`.  
  
Is it guaranteed that the sequence of async calls for `Req1` will be totally completed before the sequence of async calls for `Req2` will be started? Otherwise I risk to get response for `Req2` earlier than response for `Req1`.  Not entirely sure how does it work...

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-01-16 16:17:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1802#issuecomment-575227348  

you can't make two HTTP requests at the same time on the same socket. You have to send the first request, wait for the send to complete, then send the second request. The responses will come in the order of the requests.

---

## Comment 2

> Username: stale[bot]  
> Created at: 2020-02-15 16:44:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1802#issuecomment-586618402  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 3

> Username: stale[bot]  
> Created at: 2020-02-22 17:40:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1802#issuecomment-589980304  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
