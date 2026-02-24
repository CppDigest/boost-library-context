# #517 - Not every Linux system has /dev/fd [Open]

> Username: cmorve-te  
> Created at: 2025-09-22 15:31:58 UTC  
> Updated at: 2025-09-23 10:36:23 UTC  
> Url: https://github.com/boostorg/process/issues/517  

I have found a couple of embedded Linux devices old enough to not have close_range, and that happen to lack the /dev/fd -> /proc/self/fd symlink (AFAICT it's not required by POSIX either). They do have /proc/self/fd, though.  
  
So it would be nice if Boost.Process would try also in /proc/self/fd if `/dev/fd` fails: https://github.com/boostorg/process/blob/1bfe21baa32acc64f865c65ef5924b167de33439/src/posix/close_handles.cpp#L181

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2025-09-23 01:58:15 UTC  
> Url: https://github.com/boostorg/process/issues/517#issuecomment-3322102436  

Is `/dev/fd` only ever a syslink to `/proc/self/fd` ? I.e. could I just use the latterin all case?

---

## Comment 2

> Username: cmorve-te  
> Created at: 2025-09-23 08:04:20 UTC  
> Updated at: 2025-09-23 10:36:23 UTC  
> Url: https://github.com/boostorg/process/issues/517#issuecomment-3322857680  

/proc/self/fd is always available in Linux (as long as /proc is mounted). And yes, /dev/fd would be just a symlink there.  
  
But outside Linux /dev/fd has its own history. My understanding is that the concept originated in BSD, where it's its own directory (from a quick look I just found https://man.bsd.lv/4.4BSD-Lite2/mount_fdesc.8). Without any authoritative reference, my understanding is that it's the same in macOS and Solaris.  
  
So I guess the best solution is to use /proc/self/fd `#elif defined(__linux__)` (and close_range is not available), and keep /dev/fd for others.
