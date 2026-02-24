# #1364 - throttled_stream [Closed]

> Username: vinniefalco  
> Created at: 2018-12-10 15:14:36 UTC  
> Updated at: 2019-01-01 20:53:30 UTC  
> Closed at: 2019-01-01 20:53:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1364  

It would be nice to have a `throttled_stream` which supports optional, adjustable, individual bandwidth limits on the read and write sides.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-12-23 06:46:01 UTC  
> Url: https://github.com/boostorg/beast/issues/1364#issuecomment-449617964  

Such a stream would also have to support timeouts, since it can do so more efficiently as it already needs a timer for the throttle.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2019-01-01 20:53:30 UTC  
> Url: https://github.com/boostorg/beast/issues/1364#issuecomment-450756177  

This is finished, it is called `boost::beast::basic_stream_socket`
