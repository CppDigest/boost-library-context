# #144 - WebSocket optimization: [Closed]

> Username: vinniefalco  
> Created at: 2016-10-16 04:09:05 UTC  
> Updated at: 2017-08-04 23:11:50 UTC  
> Closed at: 2017-08-04 23:11:50 UTC  
> Url: https://github.com/boostorg/beast/issues/144  

Currently the frame header is read in using 1 or 2 system calls, followed by another system call for the frame payload. The number of system calls could be reduced considerably by issuing a call to `read_some` or `async_read_some` with a decent buffer size. This can have a big effect on performance.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-06-21 03:13:41 UTC  
> Url: https://github.com/boostorg/beast/issues/144#issuecomment-309950953  

This could use the buffer in the buffered read stream

---

## Comment 2

> Username: vinniefalco  
> Created at: 2017-06-21 03:14:02 UTC  
> Url: https://github.com/boostorg/beast/issues/144#issuecomment-309950995  

Before making any changes there needs to be a benchmarking framework set up to evaluate the difference.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-08-04 23:11:50 UTC  
> Url: https://github.com/boostorg/beast/issues/144#issuecomment-320372252  

This turned out to be VERY fruitful, reads are 3x faster now and the dynamic buffer allocation is eliminated!
