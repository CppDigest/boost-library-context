# #1503 - Test clock and timer for testing [Closed]

> Username: vinniefalco  
> Created at: 2019-03-06 13:50:22 UTC  
> Updated at: 2020-03-12 17:26:03 UTC  
> Closed at: 2020-03-12 17:26:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1503  

To enable better unit tests, we can create a user defined _Clock_ type, and then specialize `net::basic_waitable_timer` for that type. Each `io_service` can have its own independent test clock. To make full use of this, `beast::basic_stream` needs a `Clock` template parameter.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2020-03-12 17:26:02 UTC  
> Url: https://github.com/boostorg/beast/issues/1503#issuecomment-598314173  

meh...doesn't seem necessary as we have gotten this far without it...
