# #1787 - Provide a method to determine serialization state (number of bytes of the body transferred) [Open]

> Username: mika-fischer  
> Created at: 2019-12-20 18:12:27 UTC  
> Updated at: 2019-12-25 00:54:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1787  

This is the new issue I was supposed to create (https://github.com/boostorg/beast/issues/1677#issuecomment-520880599), sorry it took so long.  
  
I need a way to determine after a call to `async_write_some(stream, serializer, handler)` how many bytes of the body were written (either in that write op or in total).  
  
Note that I'm not interested in the number of bytes transferred (since that may be more due to chunked encoding), and it might be nice if this would work without setting `serializer::split(true)`, i.e. I'm not interested in the header bytes transferred...  
  
I'm open to computing the totals myself, but it must be possible to do so, which it currently isn't...
