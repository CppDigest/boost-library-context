# #2137 - built-in timer for async_read() and async_write() [Closed]

> Username: mirkub  
> Created at: 2020-12-08 10:28:48 UTC  
> Updated at: 2022-01-09 05:17:05 UTC  
> Closed at: 2022-01-09 05:17:05 UTC  
> Url: https://github.com/boostorg/beast/issues/2137  

### Version of Beast  
300  
  
### Steps necessary to reproduce the problem  
We are implement a http client which is setting up timeout for both read and write:  
  
```  
stream_.expires_after(connection_timeout_);  
http::async_read(stream_, buffer_, resp, beast::bind_front_handler(&session::on_read, shared_from_this()));  
  
....  
  
stream_.expires_after(connection_timeout_);  
http::async_write(stream_, req_, beast::bind_front_handler(&session::on_write, shared_from_this()));  
```  
  
But the program crashes in beast/core/impl/basic_stream.hpp:  
```  
723     if(! impl_->write.pending)  
724         BOOST_VERIFY(  
725             impl_->write.timer.expires_after(  
726                 expiry_time) == 0);  
```  
  
So I assume we can't mix the timeout for both read and write. How can this be sorted - i.e. how can we implement independent timeouts for read and write calls?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-12-08 15:09:22 UTC  
> Url: https://github.com/boostorg/beast/issues/2137#issuecomment-740676792  

It looks like you have a race condition in your calling code.  
The timeout works as you expect.  
Remember that no asio stream is thread-safe.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2020-12-08 17:44:09 UTC  
> Url: https://github.com/boostorg/beast/issues/2137#issuecomment-740792446  

If you change your program to be single-threaded, what happens?

---

## Comment 3

> Username: mirkub  
> Created at: 2020-12-09 09:50:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2137#issuecomment-741660446  

Moving all the logic to the ioc thread seems to have fixed the problem.  
Thanks for help.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2137#issuecomment-850857923  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 5

> Username: stale[bot]  
> Created at: 2022-01-09 05:16:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2137#issuecomment-1008232344  

It looks like this issue has either been abandoned or resolved so I will go ahead and close it. Feel free to re-open this issue if you feel it needs attention, or open new issues as needed. Thanks!
