# #865 - ConstBufferSequence mandates pointer equivalence [Closed]

> Username: vinniefalco  
> Created at: 2017-11-04 02:20:48 UTC  
> Updated at: 2017-11-04 16:51:39 UTC  
> Closed at: 2017-11-04 16:51:39 UTC  
> Url: https://github.com/boostorg/beast/issues/865  

>Yes, buffer sequences do indeed "mandate pointer equivalence" because they are specifically intended to represent a sequence of "memory regions" [buffer.reqmts.constbuffersequence]. They are functionally similar to POSIX iovec[], but with the added flexibility to use other container types. Copies still refer to the same memory regions. An asynchronous operation that accepts a Const/MutableBufferSequence argument is saying to the caller that the memory regions must remain valid until the callee operation (e.g. the OS) is done with them.  
  
Some Beast buffer sequences violate this rule (e.g. `chunk_size`).
