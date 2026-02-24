# #556 - Custom frame_buf [Closed]

> Username: vinniefalco  
> Created at: 2017-06-29 16:33:04 UTC  
> Updated at: 2017-08-06 22:41:44 UTC  
> Closed at: 2017-08-06 22:41:44 UTC  
> Url: https://github.com/boostorg/beast/issues/556  

Websocket uses `frame_streambuf` which is a `static_buffer` but it should use a custom dynamic buffer whose `const_buffer_sequence` makes a copy of the input, since the frame buffer is at most 11 bytes this makes a lot more sense, then some composed operations like `ping_op` will not need the `handler_ptr` to store state.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-29 16:34:06 UTC  
> Updated at: 2017-06-29 16:35:22 UTC  
> Url: https://github.com/boostorg/beast/issues/556#issuecomment-312021905  

We could introduce a new type `static_buffers` which has a static storage area and copies its data, and have a custom `frame_streambuf` that returns `static_buffers` as its sequence type.  
  
Actually we could use `buffers_adapter` on the `static_buffers` for serializing and then manually update its size (which can be stored using `unsigned char`)

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-08-06 22:41:43 UTC  
> Url: https://github.com/boostorg/beast/issues/556#issuecomment-320537647  

This is done now
