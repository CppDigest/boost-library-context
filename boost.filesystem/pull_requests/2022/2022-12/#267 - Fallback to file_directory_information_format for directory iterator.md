# #267 Fallback to file_directory_information_format for directory iterator [Closed]

> Username: iskiselev  
> Created at: 2022-12-01 06:19:04 UTC  
> Updated at: 2022-12-02 20:36:00 UTC  
> Closed at: 2022-12-02 13:23:55 UTC  
> Url: https://github.com/boostorg/filesystem/pull/267  

Ugly patch that fixed #266 for me

---

## Review 1 [Commented]

> Username: iskiselev  
> Created_at: 2022-12-02 20:36:00 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/267#pullrequestreview-1203050024  

📁 src/directory.cpp

```diff
 884 |+                     // Fall back to file_directory_information_format
 885 |+                     filesystem::detail::atomic_store_relaxed(g_extra_data_format, file_directory_information_format);
 886 |+                     return dir_itr_create(imp, dir, opts, params, first_filename, sf, symlink_sf);
```

> Username: iskiselev  
> Created_at: 2022-12-02 20:35:59 UTC  
> Updated_at: 2022-12-02 20:36:00 UTC  
> Url: https://github.com/boostorg/filesystem/pull/267#discussion_r1038512223  

Attempt to goto into last branch as fallback failed, most probably extra_data / dir_itr_extra_size already has some incorrect data here, have not looked deep.


---
