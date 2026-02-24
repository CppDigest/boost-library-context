# #408 Read options before parse [Merged]

> Username: jehelset  
> Created at: 2019-03-10 17:13:45 UTC  
> Updated at: 2021-10-02 21:19:09 UTC  
> Merged at: 2019-03-18 04:11:15 UTC  
> Closed at: 2019-03-18 04:11:15 UTC  
> Url: https://github.com/boostorg/build/pull/408  

* src/engine/jam.c  
 ( main ): Read KEEP_GOING and PARALLELISM before calls to parse-file, so they are respected also here.

---
