# #383 - Update the gcc-13 container to use Ubuntu 24.04 [Closed]

> Username: anarthal  
> Created at: 2024-11-27 19:50:42 UTC  
> Updated at: 2025-10-07 14:58:04 UTC  
> Closed at: 2025-10-07 14:58:03 UTC  
> Url: https://github.com/boostorg/mysql/issues/383  

The gcc-13 container currently uses Ubuntu 23.04, no longer supported. Packages can't be installed on it anymore. This is not an issue for the builds, but it'd be better to stick to LTS.

---

## Comment 1

> Username: anarthal  
> Created at: 2025-10-07 14:58:04 UTC  
> Url: https://github.com/boostorg/mysql/issues/383#issuecomment-3377308130  

Already fixed by #491
