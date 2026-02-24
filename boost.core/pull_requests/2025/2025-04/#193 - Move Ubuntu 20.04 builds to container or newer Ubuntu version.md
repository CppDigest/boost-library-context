# #193 Move Ubuntu 20.04 builds to container or newer Ubuntu version [Merged]

> Username: striezel  
> Created at: 2025-04-16 00:09:21 UTC  
> Updated at: 2025-04-16 20:34:23 UTC  
> Merged at: 2025-04-16 12:12:26 UTC  
> Closed at: 2025-04-16 12:12:26 UTC  
> Url: https://github.com/boostorg/core/pull/193  

The Ubuntu 20.04 image on GitHub Actions will be unavailable by 2025-04-15. See <https://github.com/actions/runner-images/issues/11101> for more information on the deprecation and removal.  
  
Therefore all build jobs that use the Ubuntu 20.04 runner image of GHA have to be either migrated to a newer runner image (if the compiler version is available on a newer image) or have to be moved to Docker containers using Ubuntu 20.04 (if the compiler version is not available on the newer runner images).

---
