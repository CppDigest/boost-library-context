# #2385 - What's the deal with asio::async_read & flat_buffer? [Closed]

> Username: nlyan2025  
> Created at: 2022-01-31 18:54:38 UTC  
> Updated at: 2022-06-14 17:37:52 UTC  
> Closed at: 2022-06-14 17:37:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2385  

Boost version: 1.77.0  
  
- I've been trying to use the free function asio::async_read with Beasts flat_buffer to read in length-prefixed messages from a TCP socket.   
- I am using the overload with the CompletionCondition callback to determine if we can read the length prefix, and then return the appropriate number of bytes until completion, such that a single message will be in the buffer when the read operation completes. Standard stuff.  
- Previously I used a statically sized plain old C array, and I thought flat_static_buffer would be a sane replacement.  
  
What I'm finding though is that flat_buffer/flat_static_buffer, despite modelling the DynamicBuffer_v1 concept, is fundamentally incompatible with asio::async_read's buffer ownership semantics in this scenario? Can this really be so?   
  
The algorithm itself seems sound, it calls prepare() and commit() appropriately, but because the read_dynbuf_v1_op created by async_read seems to take a copy the flat_buffer, which owns its memory, the CompletionCondition has no way to access the bytes that have been read!  
  
Move: https://github.com/boostorg/asio/blob/boost-1.77.0/include/boost/asio/impl/read.hpp#L617  
  
What's the recommended path here? Am I doing something fundamentally wrong?  
  
Is there a DynamicBuffer wrapper for an arbitrary block of memory with reference ownership semantics? Why does flat_buffer own its memory anyway?

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-01-31 18:58:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2385#issuecomment-1026104000  

Beast's dynamic buffers are not the same as Asio dynamic buffers. The former are passed by mutable reference, the latter by value.  
A Beast dynamic buffer represents actual storage, whereas an Asio dynamic buffer represents a view of storage.  
What is happening is that you are taking a copy of Beast's buffer, filling it with data, and then throwing the data away.  
  
> What I'm finding though is that flat_buffer/flat_static_buffer, despite modelling the DynamicBuffer_v1 concept, is fundamentally incompatible with asio::async_read's buffer ownership semantics in this scenario? Can this really be so?  
  
I am afraid that this is the case. We refer to Beast's dynamic buffers as DynamicBuffer_v0.  
  
I do have some work on a branch to unify the two concepts, but we never had a user require it until now so we dropped the effort.

---

## Comment 2

> Username: madmongo1  
> Created at: 2022-01-31 19:02:13 UTC  
> Url: https://github.com/boostorg/beast/issues/2385#issuecomment-1026106917  

> What's the recommended path here? Am I doing something fundamentally wrong?  
  
Unfortunately it's to write a wrapper as per...  
  
> Is there a DynamicBuffer wrapper for an arbitrary block of memory with reference ownership semantics?   
  
This.  
  
> Why does flat_buffer own its memory anyway?  
  
Beast's dynamic buffers were developed in parallel (I believe a little before) Asio's dynamic buffers. Vinnie was unconstrained by the then-standardisation effort. Chris was not.

---

## Comment 3

> Username: nlyan2025  
> Created at: 2022-01-31 19:07:59 UTC  
> Updated at: 2022-01-31 19:08:16 UTC  
> Url: https://github.com/boostorg/beast/issues/2385#issuecomment-1026112007  

Ok, thanks for the fast response. I was driving myself nuts debugging this for several hours today.  
  
I'll move to dynamic_vector_buffer and perhaps give Beasts buffers_adaptor a go for another issue I'm facing.

---

## Comment 4

> Username: madmongo1  
> Created at: 2022-01-31 19:11:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2385#issuecomment-1026114977  

A wrapper for the DynamicBuffer_V1 should be trivial as it's a passthrough.  
  
_V2 is a little more involved because the interface changed.

---

## Comment 5

> Username: joldf  
> Created at: 2022-05-24 11:54:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2385#issuecomment-1135821753  

ASIO's decision to treat buffer classes as reference-like is a real pain. I only discovered when I wrote my own buffer class (quite similar to `beast::flat_buffer`, but `prepare()` unconditionally moves memory rather than only if buffer is going to move, and also allows buffer to shrink if it gets small enough). I had given it a deleted move constructor so that caught the problem fairly quickly.  
  
As madmongo1 says, it was trivial to write a reference-based wrapper (with a single `Buffer& buffer_` member and members like `size_t size() { return buffer_.size(); }` ). As a bonus confusion, you can't even just pass an of the buffer reference class around directly because if `br` is of that type then e.g. `read_until(s, br, '\0')` will also give a compiler error (because ASIO will deduce a type as a reference to the class rather than the class itself). Instead you need to form a reference freshly each time so that it's an rvalue e.g. something like `read_until(s, buffer_ref(buf), '\0')`

---

## Comment 6

> Username: vinniefalco  
> Created at: 2022-05-24 13:25:40 UTC  
> Url: https://github.com/boostorg/beast/issues/2385#issuecomment-1135922074  

Yes it is a pain

---

## Comment 7

> Username: vinniefalco  
> Created at: 2022-06-14 17:37:51 UTC  
> Url: https://github.com/boostorg/beast/issues/2385#issuecomment-1155501917  

There's nothing we can really do about this for now.
