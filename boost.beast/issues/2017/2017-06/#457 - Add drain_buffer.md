# #457 - Add drain_buffer [Closed]

> Username: vinniefalco  
> Created at: 2017-06-09 23:22:07 UTC  
> Updated at: 2017-06-10 06:11:50 UTC  
> Closed at: 2017-06-10 06:11:50 UTC  
> Url: https://github.com/boostorg/beast/issues/457  

`drain_buffer` is a **DynamicBuffer** whose input sequence is always empty. Calls to `commit` will simply discard the data. This is useful for getting rid of the last bits at the end of a connection when one side is closed. It can come with a small fixed buffer, say 1024 bytes or something like that, to avoid memory allocation.  
  
Once this is in, we can cleanly demonstrate how to read the end of a websocket connection after receiving a close.
