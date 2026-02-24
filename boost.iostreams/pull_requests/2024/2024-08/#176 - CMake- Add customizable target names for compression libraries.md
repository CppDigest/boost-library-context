# #176 CMake: Add customizable target names for compression libraries [Merged]

> Username: tarun-t  
> Created at: 2024-08-29 14:53:51 UTC  
> Updated at: 2024-09-03 03:29:18 UTC  
> Merged at: 2024-09-02 22:52:35 UTC  
> Closed at: 2024-09-02 22:52:35 UTC  
> Url: https://github.com/boostorg/iostreams/pull/176  

- Introduce CACHE variables for ZLIB, BZip2, LibLZMA, and Zstd targets  
- Update boost_iostreams_option function calls to use new variables

---

## Comment 1

> Username: tarun-t  
> Created_at: 2024-08-30 12:40:01 UTC  
> Url: https://github.com/boostorg/iostreams/pull/176#issuecomment-2321119136  

@pdimov @mclow Drawing your attention to this PR. I am happy to do it any other way if required.

---

## Comment 2

> Username: pdimov  
> Created_at: 2024-08-30 14:31:00 UTC  
> Url: https://github.com/boostorg/iostreams/pull/176#issuecomment-2321447561  

You probably need to tell us what's the purpose of this change.

---

## Comment 3

> Username: tarun-t  
> Created_at: 2024-08-31 03:08:19 UTC  
> Url: https://github.com/boostorg/iostreams/pull/176#issuecomment-2322745202  

This change introduces configurable target names for compression libraries used by Boost.Iostreams. The main driver is to allow static linking with zstd, which isn't possible with the current hardcoded ```zstd::libzstd_shared target```. By making target names user-configurable via cache variables, we provide flexibility for different build environments and library configurations without breaking existing setups.

---

## Comment 4

> Username: pdimov  
> Created_at: 2024-08-31 09:28:49 UTC  
> Url: https://github.com/boostorg/iostreams/pull/176#issuecomment-2322841765  

Looks like this has already been proposed in https://github.com/boostorg/iostreams/pull/173.  
  
So, the only use case is changing the zstd target name from `zstd::libstd_shared` to `zstd::libstd_static`? In that case, the rest of the target variables should be removed, and I think we also need to list the values of `BOOST_IOSTREAMS_ZSTD_TARGET`.  
  
Note that these target names are referenced here  
  
https://github.com/boostorg/cmake/blob/bb741d09d2cb12f3934896880c6ecaf6de4ba707/include/BoostInstall.cmake#L367-L381  
  
so that the installed config files add the appropriate `find_dependency` call. It looks like `ztsd::libstd_static` is already handled there because of https://github.com/boostorg/cmake/pull/59.

---

## Comment 5

> Username: tarun-t  
> Created_at: 2024-08-31 11:19:38 UTC  
> Url: https://github.com/boostorg/iostreams/pull/176#issuecomment-2322866290  

Thank you for the feedback, @pdimov. I've updated the PR based on your suggestions:  
  
- Simplified the changes to focus on the zstd target name  
- Added an explicit list of possible values for BOOST_IOSTREAMS_TARGET_ZSTD  
- Implemented error checking to ensure only valid values are used  
  
This change allows users to choose between shared and static zstd libraries by setting BOOST_IOSTREAMS_TARGET_ZSTD, with proper validation to prevent incorrect values.  
  
Please let me know if you need any further modifications or clarifications.

---
