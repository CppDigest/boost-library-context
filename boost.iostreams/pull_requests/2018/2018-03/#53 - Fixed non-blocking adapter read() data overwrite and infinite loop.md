# #53 Fixed non-blocking adapter read() data overwrite and infinite loop. [Merged]

> Username: msuvajac  
> Created at: 2018-03-02 10:46:46 UTC  
> Updated at: 2018-04-15 02:47:08 UTC  
> Merged at: 2018-04-15 02:47:08 UTC  
> Closed at: 2018-04-15 02:47:08 UTC  
> Url: https://github.com/boostorg/iostreams/pull/53  

Non-blocking adapter read() wasn't tracking amount read properly which  
could cause an infinite loop when underlying (infinite) source returns amounts  
smaller than requested. Also amounts read previously were  
overwritten by subsequent calls.

---
