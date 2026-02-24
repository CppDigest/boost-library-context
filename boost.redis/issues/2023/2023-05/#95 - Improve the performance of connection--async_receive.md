# #95 - Improve the performance of connection::async_receive [Closed]

> Username: mzimbres  
> Created at: 2023-05-03 21:46:53 UTC  
> Updated at: 2023-05-28 09:14:15 UTC  
> Closed at: 2023-05-28 09:14:15 UTC  
> Url: https://github.com/boostorg/redis/issues/95  

The performance of async_receive can be easily improved with tow things  
  
1. Use `channel.cancel()` instead of `async_send`: This is going to halve the number of rescheduling needed to receive a server push.  
  
2. `async_receive` should not hand IO control to async_run (reader_op) if the read buffer contains a byte that indicates the next message is a server push.  
  
This two changes have the potential of hugely improving the performance of async_receive.
