# #353 Fixed slow pipe reading on windows [Merged]

> Username: Kojoley  
> Created at: 2018-10-11 17:35:36 UTC  
> Updated at: 2021-10-02 21:19:33 UTC  
> Merged at: 2018-10-18 02:37:23 UTC  
> Closed at: 2018-10-18 02:37:23 UTC  
> Url: https://github.com/boostorg/build/pull/353  

It looks like the OS has the internal buffer around 4KB and with any buffer  
over this size `read_pipe` will end the reading loop after first try despite  
that reading a pipe may pump a new data and it can be read immediately.  
  
Closes #307.

---

## Comment 1

> Username: grafikrobot  
> Created_at: 2018-10-18 02:38:49 UTC  
> Url: https://github.com/boostorg/build/pull/353#issuecomment-430855362  

Sorry it took a while... Had to do some local testing to make sure things worked and that it was indeed an improvement.

---
