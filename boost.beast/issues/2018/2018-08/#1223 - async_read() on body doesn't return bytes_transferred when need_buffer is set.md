# #1223 - async_read() on body doesn't return bytes_transferred when need_buffer is set [Closed]

> Username: rhashimoto  
> Created at: 2018-08-11 13:32:35 UTC  
> Updated at: 2018-12-01 15:02:00 UTC  
> Closed at: 2018-12-01 15:02:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1223  

I'm using the v124 branch because Debian is still back at Boost 62, but it looks like this behavior may also be on the trunk.  
  
I'm using [`http::async_read()`](https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/ref/boost__beast__http__async_read/overload1.html) with a parser to read a request body. I wrote a custom [`Body`](https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/concepts/Body.html) but it is very similar to [`buffer_body`](https://www.boost.org/doc/libs/1_68_0/libs/beast/doc/html/beast/ref/boost__beast__http__buffer_body.html), only using different storage. When the request body is larger than the capacity of my `Body::value_type`, my `Body::writer` (v124 is before the reader/writer swap) fills up the buffer and sets `need_buffer`. I can see that `Body::writer::put()` returns the size of the buffer, but my `http::async_read()` handler returns 0.  
  
I believe that the problem is [here](https://github.com/boostorg/beast/blob/v124/include/boost/beast/http/impl/read.ipp#L307) (in v124) and [here](https://github.com/boostorg/beast/blob/develop/include/boost/beast/http/impl/read.ipp#L313) (on the trunk). In `http::detail::read_op<>::operator()`, updating `bytes_transferred` is skipped when the underlying read returns an error. Patching my local read.ipp to move the update before the error check gives me the behavior I expect - the handler's size argument is set to the buffer size. I don't know whether this simple fix is correct for all cases, though.  
  
A workaround is to check whether `ec == boost::beast::http::need_buffer` in the handler and if so  override the size argument with the buffer size.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-08-11 14:40:15 UTC  
> Url: https://github.com/boostorg/beast/issues/1223#issuecomment-412279256  

I think you might be right, investigating...

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-10-09 17:05:23 UTC  
> Url: https://github.com/boostorg/beast/issues/1223#issuecomment-428272207  

> A workaround is to check whether ec == boost::beast::http::need_buffer in the handler  
  
That's not a good idea, because it wouldn't work if a user created their own custom error and returned it from their own body type. I think updating `bytes_transferred` even in the case of an error is the right thing to do.

---

## Comment 3

> Username: stale[bot]  
> Created at: 2018-11-27 01:09:22 UTC  
> Url: https://github.com/boostorg/beast/issues/1223#issuecomment-441859452  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: vinniefalco  
> Created at: 2018-11-27 18:20:45 UTC  
> Url: https://github.com/boostorg/beast/issues/1223#issuecomment-442163476  

Apologies for taking so long with this. I have done a thorough investigation and determined that Asio has set the precedent that when a non-success error code is indicated, the rest of the return values should be meaningful. I am applying the fix as you suggested, which passes tests and looks like an uncontroversial improvement.

---

## Comment 5

> Username: rhashimoto  
> Created at: 2018-11-27 18:26:13 UTC  
> Url: https://github.com/boostorg/beast/issues/1223#issuecomment-442165187  

No worries, I'm still using Boost 1.62 on Debian stable anyway. Thanks for fixing the trunk!

---

## Comment 6

> Username: vinniefalco  
> Created at: 2018-11-27 18:27:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1223#issuecomment-442165691  

erm...it isn't quite fixed yet :) I'll keep this open, the issue will close itself automatically once the tests pass and it gets merged.
