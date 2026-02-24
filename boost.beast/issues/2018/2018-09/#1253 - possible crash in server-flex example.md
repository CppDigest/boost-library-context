# #1253 - possible crash in server-flex example [Closed]

> Username: nazilliefesi  
> Created at: 2018-09-21 09:08:02 UTC  
> Updated at: 2018-10-07 18:28:18 UTC  
> Closed at: 2018-10-07 18:28:18 UTC  
> Url: https://github.com/boostorg/beast/issues/1253  

I was testing websocket with ssl when I encountered a crash.   
  
If an http(s) session is upgraded to websocket session The following line transfers the ownership of the stream from http(s) to websocket(ssl) session.   
https://github.com/boostorg/beast/blob/f2c3b5dec163ed2b14c5b0e86b63048f1b5a74ff/example/advanced/server-flex/advanced_server_flex.cpp#L846  
  
If after this point http session triggers a timeout, code flow eventually reaches to the point where a async_shutdown is called on the moved away stream_ object  
https://github.com/boostorg/beast/blob/f2c3b5dec163ed2b14c5b0e86b63048f1b5a74ff/example/advanced/server-flex/advanced_server_flex.cpp#L1064  
  
this is the location where I observed visual studio trigger a breakpoint with "unaccessible memory" exception.  
  
Would it be enough if i cancel the timer when transferring the stream_ to websocket_session object?   
Or is it possible that there may already be queued jobs in the io_context in which case I may keep a flag to prevent a possible call to the stream object if its in a moved away state?  
  
Regards

---

## Comment 1

> Username: nazilliefesi  
> Created at: 2018-09-21 09:52:38 UTC  
> Url: https://github.com/boostorg/beast/issues/1253#issuecomment-423479015  

It seems PR #1240 seems to fix the same problem for advanced_server_flex. If that fix is correct I may get my fix from there.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-09-23 02:20:52 UTC  
> Url: https://github.com/boostorg/beast/issues/1253#issuecomment-423786789  

Same problem as here: https://github.com/boostorg/beast/issues/1091
