# #354 Add more error handling to bp2::ext cwd.ipp. [Merged]

> Username: ghost  
> Created at: 2024-02-06 18:22:11 UTC  
> Updated at: 2024-04-04 14:02:14 UTC  
> Merged at: 2024-03-31 22:35:17 UTC  
> Closed at: 2024-03-31 22:35:17 UTC  
> Url: https://github.com/boostorg/process/pull/354  



---

## Comment 1

> Username: ghost  
> Created_at: 2024-02-06 19:24:58 UTC  
> Url: https://github.com/boostorg/process/pull/354#issuecomment-1930609340  

something tells me i didn't do this correctly at all :P @klemens-morgenstern

---

## Comment 2

> Username: ghost  
> Created_at: 2024-02-06 19:30:02 UTC  
> Url: https://github.com/boostorg/process/pull/354#issuecomment-1930616467  

@klemens-morgenstern also wanted to mention, `procstat_freefiles()`, `procstat_freeprocs()`, and `procstat_close()` all return void and seem to have no defined error reporting mentioned in the FreeBSD documentation, all the more reason for us to switch to using `libkvm` like I mentioned to you recently in a private message on the cpp lang slack.  
  
For reference: https://man.freebsd.org/cgi/man.cgi?query=libprocstat&sektion=3&format=html

---

## Review 3 [Commented]

> Username: klemens-morgenstern  
> Created_at: 2024-02-22 02:06:15 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/process/pull/354#pullrequestreview-1894731364  

📁 include/boost/process/v2/ext/impl/cwd.ipp

```diff
 189 |             BOOST_PROCESS_V2_ASSIGN_LAST_ERROR(ec)
 188 |-         pclose(fp);
 190 |+         if (pclose(fp) == -1)
```

> Username: klemens-morgenstern  
> Created_at: 2024-02-22 02:04:46 UTC  
> Updated_at: 2024-02-22 02:06:15 UTC  
> Url: https://github.com/boostorg/process/pull/354#discussion_r1498525847  

That looks to be correct.

---

📁 include/boost/process/v2/ext/impl/cwd.ipp

```diff
 154 |             BOOST_PROCESS_V2_ASSIGN_LAST_ERROR(ec)
 155 |         procstat_close(proc_stat);
 156 |+         if (errno)
```

> Username: klemens-morgenstern  
> Created_at: 2024-02-22 02:05:48 UTC  
> Updated_at: 2024-02-22 02:06:15 UTC  
> Url: https://github.com/boostorg/process/pull/354#discussion_r1498526484  

Is this i a guess, or do you know this resets `errno` if it succeeds? Because it might just be a function that never fails.

> Username: Unknown  
> Created_at: 2024-02-22 04:12:34 UTC  
> Url: https://github.com/boostorg/process/pull/354#discussion_r1498613155  

for other people reading this pull request who don't know what i told klemens on slack, these functions never fail and are just wrappers around the free() function, after inspecting FreeBSD's source tree for libprocstat's source code.


---
