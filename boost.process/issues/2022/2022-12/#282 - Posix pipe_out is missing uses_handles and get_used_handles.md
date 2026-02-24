# #282 - Posix pipe_out is missing uses_handles and get_used_handles [Open]

> Username: suokko  
> Created at: 2022-12-12 19:46:46 UTC  
> Updated at: 2024-04-17 14:03:15 UTC  
> Url: https://github.com/boostorg/process/issues/282  

I'm using boost 1.80. I haven't checked if a later development version includes a fix already.  
  
I noticed that file handles were leaking to exec children. This caused some parallel children's hand because leaked pipes stayed open after the source process had exited. When I was trying to figure out how to close the extra file descriptor I noticed limit_handles. limit_handles promises to prevent file descriptors from leaking. Too bad it seemed to break all pipes for children. I managed to figure out that the issue was hidden in the posix pipe_out implementation.  All pipe_out file descriptors are being closed.  
  
I tested locally that inheriting from uses_handles and implementing get_used_handles makes my code work.

---

## Comment 1

> Username: dkl  
> Created at: 2024-04-17 14:03:13 UTC  
> Url: https://github.com/boostorg/process/issues/282#issuecomment-2061341897  

Looks like this was fixed by commit 768944672f997895baf074560f74fe6680860486 for boost 1.85.0
