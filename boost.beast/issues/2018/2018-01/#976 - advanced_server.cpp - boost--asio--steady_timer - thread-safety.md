# #976 - advanced_server.cpp / boost::asio::steady_timer / thread-safety [Closed]

> Username: rberlich  
> Created at: 2018-01-11 19:30:57 UTC  
> Updated at: 2018-01-12 13:56:15 UTC  
> Closed at: 2018-01-12 11:00:17 UTC  
> Url: https://github.com/boostorg/beast/issues/976  

Hi there,  
  
from what I can see, the advanced_server.cpp example extends the lifetime of the boost::asio::steady_timer object both in on_timer() and in the activity()-function, which is called from on_control_callback(). As steady_timer is [not thread-safe](http://www.boost.org/doc/libs/1_53_0/doc/html/boost_asio/reference/steady_timer.html) for shared objects and it is unpredictable, when on_control_callback() is executed, this may be a race condition or result in undefined behavior (unless there is some implicit locking or strand).  
  
Kind Regards,  
Beet

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-01-12 05:52:00 UTC  
> Url: https://github.com/boostorg/beast/issues/976#issuecomment-357149571  

The control callback is executed using the same executor as that used to invoke the final completion handler, see the **Note** on this page:  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_websocket/control_frames.html#beast.using_websocket.control_frames.control_callback  
  
You can also confirm by looking at the code:  
https://github.com/boostorg/beast/blob/6f08814a0c291eb9f03aaa1daefffdc45c1b9087/include/boost/beast/websocket/impl/read.ipp#L277

---

## Comment 2

> Username: rberlich  
> Created at: 2018-01-12 11:00:17 UTC  
> Url: https://github.com/boostorg/beast/issues/976#issuecomment-357209144  

Hmmm, and I have stated something as a fact which obviously isn't :-)  Thanks for the heads-up!  
Beet

---

## Comment 3

> Username: vinniefalco  
> Created at: 2018-01-12 13:56:00 UTC  
> Updated at: 2018-01-12 13:56:15 UTC  
> Url: https://github.com/boostorg/beast/issues/976#issuecomment-357244354  

Well to be fair - there **was** a bug in the invocation of the control callback where it could bypass the executor if there was a control frame in the read buffer, I fixed it on December 27th:  
https://github.com/boostorg/beast/commit/eddadacba7fe4cd00cbc58ebc72e3820cdd654ef
