# #52 Fixed bzip2 memory leak. [Merged]

> Username: msuvajac  
> Created at: 2018-03-02 09:09:14 UTC  
> Updated at: 2018-04-14 22:33:18 UTC  
> Merged at: 2018-04-14 22:33:18 UTC  
> Closed at: 2018-04-14 22:33:18 UTC  
> Url: https://github.com/boostorg/iostreams/pull/52  

When error is encounted during compression/decompression exception is  
thrown (through bzip2::check()) but memory associated with the bzip2 stream  
wasn't properly released (BZ2_bzCompressEnd()/BZ2_bzDecompressEnd() must be  
called to do that - which is done by calling bzip2_base::end()).  
As end() throws on error, and we are now calling end() in destructor, nothrow  
version needed to be made so we don't terminate user program in case end()  
fails.

---
