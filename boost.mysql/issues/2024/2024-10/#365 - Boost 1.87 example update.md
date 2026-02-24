# #365 - Boost 1.87 example update [Closed]

> Username: anarthal  
> Created at: 2024-10-05 12:37:07 UTC  
> Updated at: 2025-02-11 19:02:10 UTC  
> Closed at: 2025-02-11 19:02:08 UTC  
> Url: https://github.com/boostorg/mysql/issues/365  

In 1.87, any_connection and SQL formatting are stable, so examples should be updated to reflect these new best practices. Now that C++20 coroutines are more widespread and that we support them cleanly thanks to with_diagnostics, most examples should be updated to use these. Some need to be reviewed (as they were written long ago).  
  
I propose having simple-but-complete examples demonstrating a single feature (e.g. suppressing exceptions, using pipelines, avoiding copies in SQL formatting, metadata...) plus a couple of fully-fledged web servers. In particular, I dislike the current examples about orders, because they don't fit any of these. Some ETL-like examples would be nice, too.

---

## Comment 1

> Username: anarthal  
> Created at: 2024-11-16 22:13:31 UTC  
> Url: https://github.com/boostorg/mysql/issues/365#issuecomment-2480827783  

This was incorrectly closed by GitHub.

---

## Comment 2

> Username: anarthal  
> Created at: 2025-02-11 19:02:08 UTC  
> Url: https://github.com/boostorg/mysql/issues/365#issuecomment-2651805074  

This has been added as sub-issues of #366.
