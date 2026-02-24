# #498 Downgrade `run-vcpkg` to avoid Windows build regression. [Merged]

> Username: stefanseefeld  
> Created at: 2025-10-24 14:58:32 UTC  
> Updated at: 2025-10-25 17:25:53 UTC  
> Merged at: 2025-10-25 17:25:52 UTC  
> Closed at: 2025-10-25 17:25:52 UTC  
> Url: https://github.com/boostorg/python/pull/498  

@e-kwsm , I need to revert your upgrade of the `run-vcpkg` package in the Windows CI pipeline. I'm not exactly sure what was broken, but it generated an error, and ultimately failed the build.  
  
As this is now making all builds pass, I'd suggest that you rebase all your other PRs so we can merge them once the CI builds have succeeded.

---

## Comment 1

> Username: e-kwsm  
> Created_at: 2025-10-24 18:06:47 UTC  
> Url: https://github.com/boostorg/python/pull/498#issuecomment-3444305703  

Alright, I don&rsquo;t know how run-vcpkg works, there must have been breaking changes at some point.

---
