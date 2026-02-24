# #289 ec fix for search_path with std::filesystem. [Merged]

> Username: klemens-morgenstern  
> Created at: 2022-12-24 03:17:12 UTC  
> Updated at: 2023-02-06 11:09:39 UTC  
> Merged at: 2023-02-06 11:09:38 UTC  
> Closed at: 2023-02-06 11:09:38 UTC  
> Url: https://github.com/boostorg/process/pull/289  

closes #287.

---

## Review 1 [Commented]

> Username: stuomas  
> Created_at: 2023-01-03 12:35:16 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/process/pull/289#pullrequestreview-1234487243  

📁 include/boost/process/detail/posix/search_path.hpp

```diff
  28 |     {
  29 |         auto p = pp / filename;
  30 |+ #if defiend(BOOST_PROCESS_USE_STD_FS)
```

> Username: stuomas  
> Created_at: 2023-01-03 12:35:16 UTC  
> Url: https://github.com/boostorg/process/pull/289#discussion_r1060544556  

typo defiend -> defined


---

## Review 2 [Commented]

> Username: stuomas  
> Created_at: 2023-01-03 12:35:25 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/process/pull/289#pullrequestreview-1234487426  

📁 include/boost/process/detail/windows/search_path.hpp

```diff
  62 |             boost::process::filesystem::path pp_ext = p;
  63 |             pp_ext += ext;
  64 |+ #if defiend(BOOST_PROCESS_USE_STD_FS)
```

> Username: stuomas  
> Created_at: 2023-01-03 12:35:25 UTC  
> Url: https://github.com/boostorg/process/pull/289#discussion_r1060544683  

typo defiend -> defined


---
