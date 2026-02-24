# #1714 - What is the websocket's state if async_close fails? [Closed]

> Username: leleftheriades  
> Created at: 2019-10-02 15:03:11 UTC  
> Updated at: 2019-12-20 17:20:12 UTC  
> Closed at: 2019-12-20 17:20:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1714  

Beast Version 1.70  
OpenSSL version 1.1.1a  
  
We use Websocket over SSL. A great number of times when calling `async_close`, the operation fails with error code 1 and message "stream truncated".  
  
We are wondering what the state of the websocket is after the `async_close` has failed.  
is it open, closed or undefined?  
  
We found that in some instances a pending `async_read` operation was not cancelled after the `async_close` was run and failed. In some other instances the `async_read` operation was indeed cancelled.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-10-02 15:13:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-537540465  

Reproducing this in a live run is practically impossible, so we will need to figure out how to make it happen reliably in a test scenario. I would advise that you look at the existing websocket tests to see how they work, and look at the websocket implementation to find where you think that the async read is not being canceled on a failure. Then set up a test that reproduces this situation.  
  
When an async operation fails, the function `check_stop_now` is called which inspects the error and triggers an early exit from the operation. For the async close operation you can see that here:  
https://github.com/boostorg/beast/blob/develop/include/boost/beast/websocket/impl/close.hpp#L109

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-10-02 15:14:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-537540821  

The status of the websocket should be `status::closed` or `status::failed`:  
https://github.com/boostorg/beast/blob/0ca107bb5c2c1d25fd9b1cd3b44a548afbfd121d/include/boost/beast/websocket/impl/stream_impl.hpp#L329

---

## Comment 3

> Username: leleftheriades  
> Created at: 2019-10-03 08:45:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-537849887  

To be honest we are not sure if async_close completed with an error, or it never completed after 10 minutes. Our logs are mixed in the same file. We have improved our logging and will post an update if we have more data.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2019-11-02 09:10:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-549025740  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: vinniefalco  
> Created at: 2019-11-02 14:35:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-549049221  

Try giving this a spin? https://github.com/boostorg/beast/pull/1754

---

## Comment 6

> Username: leleftheriades  
> Created at: 2019-11-06 10:12:27 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-550241352  

Hello, it just happened again today. According to our logs we called async_close and it never completed (after 5 minutes). Our versions are beast 1.71 and OpenSSL 1.1.1d.  
We did not have any calls to set_option, so we never set a timeout on the websocket. We also called expires_never() before upgrading out httpsession to websocket. So we don't believe it is related to a timeout.

---

## Comment 7

> Username: vinniefalco  
> Created at: 2019-11-06 12:52:49 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-550296057  

There is no guarantee that `async_close` will complete. If the peer never closes the connection, then the completion handler is never invoked, beast will wait patiently forever. That's why you need a timeout. This is explained in the websocket RFC, see 5.5.1: https://tools.ietf.org/html/rfc6455

---

## Comment 8

> Username: leleftheriades  
> Created at: 2019-11-06 13:18:49 UTC  
> Updated at: 2019-11-06 13:39:41 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-550305027  

What is the difference between async_close and async_teardown? Which one(s) should we use to close the websoket gracefuly?  
  
Our stream type is: boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::tcp_stream>>

---

## Comment 9

> Username: vinniefalco  
> Created at: 2019-11-06 13:39:47 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-550312754  

`stream::async_close`

---

## Comment 10

> Username: leleftheriades  
> Created at: 2019-11-06 13:42:59 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-550314015  

We came across this note:  
To receive the error::closed error, a read operation is required.  
in [control_frames](https://www.boost.org/doc/libs/1_71_0/libs/beast/doc/html/beast/using_websocket/control_frames.html) document.  
Does that mean we have to have a pending async_read operation active, or is that handled by beast?

---

## Comment 11

> Username: vinniefalco  
> Created at: 2019-11-06 13:48:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-550316119  

It seems the doc is a tiny bit misleading. If you call `async_close` and there is no read pending, beast will read for you. The note in the doc refers to the scenario where you are not calling `async_close`, and the peer sends a close frame.

---

## Comment 12

> Username: leleftheriades  
> Created at: 2019-11-06 14:30:19 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-550334650  

Thank you so much for your help!

---

## Comment 13

> Username: stale[bot]  
> Created at: 2019-12-06 14:53:24 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-562601717  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 14

> Username: stale[bot]  
> Created at: 2019-12-13 16:50:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-565515787  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!

---

## Comment 15

> Username: stale[bot]  
> Created at: 2019-12-20 17:20:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1714#issuecomment-568007376  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
