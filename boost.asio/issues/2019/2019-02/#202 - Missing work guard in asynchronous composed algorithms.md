# #202 - Missing work guard in asynchronous composed algorithms [Closed]

> Username: djarek  
> Created at: 2019-02-15 00:49:42 UTC  
> Updated at: 2020-12-30 00:59:58 UTC  
> Closed at: 2020-12-30 00:59:57 UTC  
> Url: https://github.com/boostorg/asio/issues/202  

Algorithms that are composed operations and call more than one `async_*` suboperation (`boost::asio::async_read`, `boost::asio::async_write`, etc.) are missing an I/O executor `executor_work_guard` non-static data member.

---

## Comment 1

> Username: mabrarov  
> Created at: 2019-02-15 11:31:29 UTC  
> Url: https://github.com/boostorg/asio/issues/202#issuecomment-464012380  

Test reproducing this issue can be found at https://github.com/mabrarov/asio_samples/blob/feature/Beast_1450_no_work_guard_handler_with_custom_executor/examples/beast_echo_op/src/echo_op.cpp#L321

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 00:59:56 UTC  
> Url: https://github.com/boostorg/asio/issues/202#issuecomment-752291158  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#673](https://github.com/chriskohlhoff/asio/issues/673).
