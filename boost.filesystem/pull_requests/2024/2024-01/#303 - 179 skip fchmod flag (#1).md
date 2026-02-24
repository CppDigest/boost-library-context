# #303 179 skip fchmod flag (#1) [Closed]

> Username: dsingh-mw  
> Created at: 2024-01-14 11:27:07 UTC  
> Updated at: 2024-10-01 22:20:54 UTC  
> Closed at: 2024-10-01 22:20:54 UTC  
> Url: https://github.com/boostorg/filesystem/pull/303  

Added test for the flag "ignore_attribute_errors"

---

## Comment 1

> Username: Lastique  
> Created_at: 2024-01-14 11:53:19 UTC  
> Updated_at: 2024-01-14 12:14:29 UTC  
> Url: https://github.com/boostorg/filesystem/pull/303#issuecomment-1890929970  

I don't think there's a good way to test this. Replacing `fchmod` (a) requires relying on specific linker behavior, (b) may affect code other than `boost::filesystem::copy_file` and (c) relies on that `copy_file` actually uses `fchmod` internally. Neither of these I like, so I'm not going to merge this.

---
