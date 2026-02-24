# #217 Use only node16 in CI [Merged]

> Username: jefftrull  
> Created at: 2024-08-27 04:00:23 UTC  
> Updated at: 2024-08-27 04:48:52 UTC  
> Merged at: 2024-08-27 04:48:52 UTC  
> Closed at: 2024-08-27 04:48:52 UTC  
> Url: https://github.com/boostorg/wave/pull/217  

To test against different compiler versions we use older Docker images, which in turn have different glibc versions. Github recently deprecated Node version 16, which had been built with an older glibc version, and the newer Node version requires a glibc version that is not available on the old images, causing builds to fail.  
  
This is a hack permitting the continued use of Node 16, until Github decides to make it impossible.

---
