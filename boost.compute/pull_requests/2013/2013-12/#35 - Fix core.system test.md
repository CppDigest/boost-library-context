# #35 Fix core.system test [Merged]

> Username: ddemidov  
> Created at: 2013-12-27 07:39:18 UTC  
> Updated at: 2013-12-28 17:06:16 UTC  
> Merged at: 2013-12-28 17:05:12 UTC  
> Closed at: 2013-12-28 17:05:12 UTC  
> Url: https://github.com/boostorg/compute/pull/35  

The find_device check in core.system is invalid. It could fail when same device is supported by several platforms. In my case this happens for Intel CPU when both AMD and Intel platforms are installed. The CPU returned by `boost::compute::system::default_device()` is served by the AMD platform, and the CPU returned by  
`boost::compute::system::find_device(name)` is served by the Intel SDK. The only thing that could be safely asserted here is that both devices have the same name.

---

## Comment 1

> Username: kylelutz  
> Created_at: 2013-12-28 17:05:30 UTC  
> Url: https://github.com/boostorg/compute/pull/35#issuecomment-31300321  

Looks good. Merged.

---
