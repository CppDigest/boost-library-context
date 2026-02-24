# #297 Revert change to elide a warning that caused Solaris builds to fail. [Merged]

> Username: datalogics-robb  
> Created at: 2019-11-20 17:27:29 UTC  
> Updated at: 2019-12-11 06:29:38 UTC  
> Merged at: 2019-12-11 06:29:38 UTC  
> Closed at: 2019-12-11 06:29:38 UTC  
> Url: https://github.com/boostorg/thread/pull/297  

Revert a minor change to elide a warning regarding comparison of unsigned to zero that caused solaris platforms to fail to build.  
  
https://github.com/boostorg/thread/issues/283

---

## Comment 1

> Username: datalogics-robb  
> Created_at: 2019-11-20 20:09:09 UTC  
> Url: https://github.com/boostorg/thread/pull/297#issuecomment-556330572  

There is a single CI failure, 388.10 - but that doesn't look like it could be related to the change in this PR.

---
