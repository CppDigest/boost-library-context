# #1305 - Support move members in dynamic buffers where possible [Closed]

> Username: reddwarf69  
> Created at: 2018-11-16 11:00:26 UTC  
> Updated at: 2018-12-01 15:02:00 UTC  
> Closed at: 2018-12-01 15:02:00 UTC  
> Url: https://github.com/boostorg/beast/issues/1305  

I was writing a composed operation that "registers"/"connects"/"handshakes". There are three steps in the operation:  
1) A write sending some registration info  
2) A read to read the result  
3) A parsing of the read message so the "main handler" can be called simply saying registration "success" or "failure" without forcing the caller to parse the message (i.e. my handler is of the form `void(error_code)` and error_code reports failure even if it *correctly* receives the reply... saying it does *not* accept my request)  
  
So one of the members of my compose operation is `beast::flat_buffer buffer_;`. No problems using it to write in the first step. Then in the second step I do something like:  
  
`return stream_.async_read_some(buffer_.prepare(N), std::move(*this));` (but doing the `prepare` before to avoid undefined behavior)  
  
Everything seems to go fine, it calls itself and the `bytes_written` parameter of the handler is `N` as expected.  
  
Then in the third step I call `mBuffer.commit(bytes_written);` and `buffer_.data()` to parse it... and it says its size is zero!  
  
  
After reading the docs (https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__basic_flat_buffer/basic_flat_buffer/overload5.html) the problem becomes evident: `After the move, *this will have an empty output sequence.`  
  
  
Now... while well documented, this behavior was quite surprising to me. I was expecting something more like a std::vector... the pointer to the memory is the only thing copied and `other` becomes invalid. I'm guessing the special move behaviour is there to support some user case I can't think of? Would it be possible to somehow also support my user case?  
  
I obviously can handle my case using a beast::handler_ptr to store the buffer, but I would have liked to avoid it if there was no other need for it (my state is not `expensive or impossible to move`).  
My `N` is just 6 bytes. So I can just call buffer_.commit(`N`) before calling `async_read_some()` and the 6 bytes get copied when doing the move. But that's even technically incorrect since supposedly after calling `commit()` `All previous buffers sequences obtained from calls to ... basic_flat_buffer::prepare are invalidated.`.  
  
So... I would have really liked beast::flat_buffer to have acted as std::vector when moving. And I'm not really sure why it isn't.  
  
I can see that multi_buffer behaves the same way. And, FWIW there seems to be some problem in the generated flat_buffer documentation. While https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__basic_multi_buffer.html says `Move constructor` https://www.boost.org/doc/libs/develop/libs/beast/doc/html/beast/ref/boost__beast__basic_flat_buffer.html only says `Constructor`.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-11-29 20:58:05 UTC  
> Url: https://github.com/boostorg/beast/issues/1305#issuecomment-442990616  

Working on it...
