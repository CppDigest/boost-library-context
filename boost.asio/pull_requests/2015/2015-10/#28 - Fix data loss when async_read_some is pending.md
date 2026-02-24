# #28 Fix data loss when async_read_some is pending [Open]

> Username: amedama41  
> Created at: 2015-10-25 08:18:10 UTC  
> Updated at: 2015-10-25 08:18:10 UTC  
> Url: https://github.com/boostorg/asio/pull/28  

When `ssl::stream::async_read_some` is pending, a cancel error, which is occured by `pending_read_`'s cancel,  is passed to a handler while some data is read from a SSL instance.  
So, error code should be cleared.  
In addition, `pending_read_` and `pending_write_` should be modified by an `io_op` instance which actually called IO operation, in order to be only one reading or writing operation is invoked.  
  
There is another issue for the pending handling. Handlers for pending writing are called before the buffers corresponding to handlers are actually writen to underlying stream. But I have no idea for the issue.

---
