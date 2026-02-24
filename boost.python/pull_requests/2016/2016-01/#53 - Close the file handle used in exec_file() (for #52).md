# #53 Close the file handle used in exec_file() (for #52) [Open]

> Username: hotgloupi  
> Created at: 2016-01-08 10:55:14 UTC  
> Updated at: 2020-11-14 02:30:48 UTC  
> Url: https://github.com/boostorg/python/pull/53  

For python >= 3, a plain `FILE*` is used, whereas for python2, an object handle takes care of the file object lifetime.  
This commit manually closes the file handle after the `PyRun_File()` call.

---

## Comment 1

> Username: paralin  
> Created_at: 2016-02-26 03:17:47 UTC  
> Url: https://github.com/boostorg/python/pull/53#issuecomment-189091774  

I still have this problem, can this be merged soon?

---

## Comment 2

> Username: paralin  
> Created_at: 2016-02-26 03:39:19 UTC  
> Url: https://github.com/boostorg/python/pull/53#issuecomment-189098402  

Actually for some reason this causes an access violation:  
  
`0xC0000005: Access violation writing location 0x00000014.`  
  
So.. maybe doesn't work quite right :)

---

## Comment 3

> Username: hotgloupi  
> Created_at: 2016-02-26 08:25:08 UTC  
> Url: https://github.com/boostorg/python/pull/53#issuecomment-189159639  

Can you reproduce easily ? Do you have a backtrace ?

---

## Comment 4

> Username: paralin  
> Created_at: 2016-02-26 20:05:30 UTC  
> Url: https://github.com/boostorg/python/pull/53#issuecomment-189459522  

@hotgloupi I undid the patch and don't have a backtrace. Under python 3.4 however it seems the file descriptor was cleaned up already, resulting in multiple fdclose calls on the descriptor. I didn't have too much time to look into it.

---
