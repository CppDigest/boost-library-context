# #762 ci: move Ubuntu 20.04 builds to container or newer Ubuntu version [Merged]

> Username: striezel  
> Created at: 2025-04-04 17:06:14 UTC  
> Updated at: 2025-08-03 18:07:05 UTC  
> Merged at: 2025-04-04 20:03:43 UTC  
> Closed at: 2025-04-04 20:03:43 UTC  
> Url: https://github.com/boostorg/gil/pull/762  

### Description  
  
The Ubuntu 20.04 image on GitHub Actions will be unavailable by 2025-04-15. See <https://github.com/actions/runner-images/issues/11101> for more information on the deprecation and removal.  
  
Therefore all build jobs that use the Ubuntu 20.04 runner image of GHA have to be either migrated to a newer runner image (if the compiler version is available on a newer image) or have to be moved to Docker containers using Ubuntu 20.04 (if the compiler version is not available on the newer runner images). And that's what this pull request tries to do.  
  
### References  
  
https://github.com/actions/runner-images/issues/11101  
  
### Tasklist  
  
- [x] Ensure all modified CI builds pass  
- [x] Review and approve

---

## Comment 1

> Username: striezel  
> Created_at: 2025-04-04 17:30:29 UTC  
> Url: https://github.com/boostorg/gil/pull/762#issuecomment-2779354737  

Not sure why the Clang 5 build on Linux and the Clang build on macOS are failing here, because those CI jobs have not been changed. :thinking:

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2025-04-04 20:03:32 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/762#pullrequestreview-2743987541  

Thanks a lot!

---
