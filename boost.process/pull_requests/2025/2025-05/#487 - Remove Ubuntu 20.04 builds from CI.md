# #487 Remove Ubuntu 20.04 builds from CI [Merged]

> Username: striezel  
> Created at: 2025-05-03 21:52:00 UTC  
> Updated at: 2025-05-05 14:37:32 UTC  
> Merged at: 2025-05-05 14:30:26 UTC  
> Closed at: 2025-05-05 14:30:27 UTC  
> Url: https://github.com/boostorg/process/pull/487  

The Ubuntu 20.04 image on GitHub Actions has been unavailable since 2025-04-15. See <https://github.com/actions/runner-images/issues/11101> for more information on the deprecation and removal.  
  
Therefore all build jobs that use the Ubuntu 20.04 runner image of GHA will fail and have to be replaced by newer images or have to move into Ubuntu 20.04 containers.

---

## Comment 1

> Username: klemens-morgenstern  
> Created_at: 2025-05-05 14:30:31 UTC  
> Url: https://github.com/boostorg/process/pull/487#issuecomment-2851202270  

Thank you

---
