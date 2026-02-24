# #1682 - BOOST_ASSERT(! b_) [Closed]

> Username: leleftheriades  
> Created at: 2019-08-14 08:08:49 UTC  
> Updated at: 2019-08-29 21:42:00 UTC  
> Closed at: 2019-08-29 21:42:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1682  

Being beginners with using the library and not understanding the full implications of issuing a second async_write on the same websocket/stream before the first one finishes we noticed that our program crashed at   
  
[BOOST_ASSERT(! b_) of stream_base.hpp](https://github.com/boostorg/beast/blob/develop/include/boost/beast/core/detail/stream_base.hpp#L73)  
  
It would be helpful if a more descriptive error would show up, or at least a comment in the code to indicate the probable causes of this error

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-08-29 17:54:11 UTC  
> Url: https://github.com/boostorg/beast/issues/1682#issuecomment-526293801  

I agree! This was an oversight.
