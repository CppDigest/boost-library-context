# #52 Changes still_active constant to support Solaris. [Merged]

> Username: jake-at-work  
> Created at: 2018-09-27 16:08:48 UTC  
> Updated at: 2018-09-27 17:25:21 UTC  
> Merged at: 2018-09-27 17:04:08 UTC  
> Closed at: 2018-09-27 17:04:09 UTC  
> Url: https://github.com/boostorg/process/pull/52  

The use of the WIFSTOPPED bits did not go far enough on Solaris. It  
is expected to also have a non-zero value in the next bytes. This  
new value is tested be valid on Linux, MacOS and Solaris.  
  
Fixes #51

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2018-09-27 17:03:48 UTC  
> Updated_at: 2018-09-27 17:04:36 UTC  
> Url: https://github.com/boostorg/process/pull/52#issuecomment-425168681  

Awesome. Yeah, the exit code is garbage before the program has exited anyhow, so the 1 doesn't hurt. Thanks, that's really elegant if it solves your solatis issue.

---

## Comment 2

> Username: jake-at-work  
> Created_at: 2018-09-27 17:25:21 UTC  
> Url: https://github.com/boostorg/process/pull/52#issuecomment-425175545  

Thanks! So far so good.

---
