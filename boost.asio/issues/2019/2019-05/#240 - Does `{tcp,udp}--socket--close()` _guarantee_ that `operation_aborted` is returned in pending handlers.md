# #240 - Does `{tcp,udp}::socket::close()` _guarantee_ that `operation_aborted` is returned in pending handlers? [Closed]

> Username: inetic  
> Created at: 2019-05-17 07:20:55 UTC  
> Updated at: 2020-12-30 01:04:25 UTC  
> Closed at: 2020-12-30 01:04:24 UTC  
> Url: https://github.com/boostorg/asio/issues/240  

I feel there is some inconsistency in the documentation. In particular, the documentation for [`basic_stream_socket::close`](https://www.boost.org/doc/libs/1_70_0/doc/html/boost_asio/reference/basic_stream_socket/close/overload1.html) says:  
  
(A)  
  
> This function is used to close the socket. Any asynchronous send, receive or connect operations will be cancelled immediately, and will complete with the `boost::asio::error::operation_aborted` error.  
  
Which I would normally understand that the guarantee that my handlers will be executed with the error is indeed there.  
  
__But__, the documentation for [`basic_deadline_timer::cancel`](https://www.boost.org/doc/libs/1_70_0/doc/html/boost_asio/reference/basic_deadline_timer/cancel/overload1.html) says pretty much the same thing:  
  
(B)  
  
> The handler for each cancelled operation will be invoked with the boost::asio::error::operation_aborted error code  
  
Which I would normally understand the same.  
  
__However__, it also adds the following "Remark":  
  
(C)  
  
> If the timer has already expired when cancel() is called, then the handlers for asynchronous wait operations will:  
> * have already been invoked; or  
> * have been queued for invocation in the near future.  
>   
> These handlers can no longer be cancelled, and therefore are passed an error code that indicates the successful completion of the wait operation.  
  
Which seems to be in a direct contradiction with the statement (B).  
  
And if the two (B and C) statements do _not_ contradict each other, then that implies that the statement (B) does _not_ guarantee the return of `operation_aborted`. Finally, given that the statements (A) and (B) are conceptually the same, that IMO implies that (A) does not guarantee it neither. But there is no such remark like (C) in the `basic_stream_socket::close` function documentation.

---

## Comment 1

> Username: djarek  
> Created at: 2019-05-17 14:02:13 UTC  
> Url: https://github.com/boostorg/asio/issues/240#issuecomment-493464844  

This is a bit of tricky wording, the key is to understand that only outstanding operations can be cancelled, check the wording  in the [Networking TS](https://timsong-cpp.github.io/cppwp/networking-ts/socket.basic.ops#16).    
  
In general, Async Operations are in either of 3 states:  
- Outstanding  
- Completed (i.e. completion handler submitted for execution)  
- Completion handler invoked.  
  
Cancellation can only affect operations in the first state.

---

## Comment 2

> Username: ghost  
> Created at: 2020-12-30 01:04:23 UTC  
> Url: https://github.com/boostorg/asio/issues/240#issuecomment-752292123  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#691](https://github.com/chriskohlhoff/asio/issues/691).
