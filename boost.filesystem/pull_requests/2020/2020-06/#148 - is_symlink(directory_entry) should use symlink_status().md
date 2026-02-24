# #148 is_symlink(directory_entry) should use symlink_status(). [Merged]

> Username: kanje  
> Created at: 2020-06-04 20:12:31 UTC  
> Updated at: 2020-06-04 20:24:37 UTC  
> Merged at: 2020-06-04 20:24:29 UTC  
> Closed at: 2020-06-04 20:24:29 UTC  
> Url: https://github.com/boostorg/filesystem/pull/148  

This fixes a problem that is_symlink(directory_entry) always returns  
false, even if directory_entry is indeed a symlink. This change makes  
is_symlink(directory_entry) behave the same as is_symlink(path) and  
use symlink_status().

---

## Comment 1

> Username: Lastique  
> Created_at: 2020-06-04 20:24:37 UTC  
> Url: https://github.com/boostorg/filesystem/pull/148#issuecomment-639096756  

Thanks.

---
