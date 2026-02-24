# #194 - Is it possible to bind a strand to a specific thread? [Closed]

> Username: traceon  
> Created at: 2019-01-13 10:40:14 UTC  
> Updated at: 2020-12-30 00:59:28 UTC  
> Closed at: 2020-12-30 00:59:27 UTC  
> Url: https://github.com/boostorg/asio/issues/194  

Is there any known technique to force all tasks of a specific strand to be executed only in a predefined thread(s) (that is, from run() or similar call, called on a specific thread.)

---

## Comment 1

> Username: djarek  
> Created at: 2019-01-13 16:34:58 UTC  
> Url: https://github.com/boostorg/asio/issues/194#issuecomment-453844284  

The only way of making sure that a completion handler runs on a specific thread is to make sure that it runs in an implicit strand (i.e. one `io_context` per thread).

---

## Comment 2

> Username: traceon  
> Created at: 2019-01-13 17:43:11 UTC  
> Url: https://github.com/boostorg/asio/issues/194#issuecomment-453850209  

I see, thank you @djarek

---

## Comment 3

> Username: ghost  
> Created at: 2020-12-30 00:59:26 UTC  
> Url: https://github.com/boostorg/asio/issues/194#issuecomment-752291085  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#668](https://github.com/chriskohlhoff/asio/issues/668).
