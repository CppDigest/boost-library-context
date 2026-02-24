# #55 some directory_entry overloads for some status query functions [Closed]

> Username: emmett-b  
> Created at: 2017-10-18 12:32:40 UTC  
> Updated at: 2018-11-24 21:20:22 UTC  
> Closed at: 2018-11-24 17:54:11 UTC  
> Url: https://github.com/boostorg/filesystem/pull/55  

Without these functions, calling (for example) `is_directory` with a `directory_entry` results in:  
- a conversion to `path` using `operator const boost::filesystem::path&()`,  
- then a call to `is_directory(const path& p)` which recomputes the status with `detail::status(p)`.  
    
These functions avoid a costly recomputation of the status if one calls `is_directory(e)` instead of `is_directory(e.status)`

---

## Comment 1

> Username: Lastique  
> Created_at: 2018-11-24 17:54:11 UTC  
> Url: https://github.com/boostorg/filesystem/pull/55#issuecomment-441384527  

Thanks. Committed 4731ce79de213ed9c75cfc3b125a0626efd57f07 with some modifications.

---

## Comment 2

> Username: emmett-b  
> Created_at: 2018-11-24 21:20:22 UTC  
> Url: https://github.com/boostorg/filesystem/pull/55#issuecomment-441396481  

Great! Thanks!

---
