# #50 Fixes static initialization fiasco crash [Closed]

> Username: Klaim  
> Created at: 2017-08-15 14:19:22 UTC  
> Updated at: 2018-01-04 13:48:27 UTC  
> Closed at: 2018-01-04 13:48:27 UTC  
> Url: https://github.com/boostorg/filesystem/pull/50  

In an application I have something like that:  
  
    namespace blah {  
        // using boost filesystem types:  
        const path CACHE_DIR = system_complete( temp_directory_path() / app_directory_name );  
    }  
  
This ends up calling `make_permissions()` (the modified function of this patch) with `equal_string_ordinal_ic` being null in Debug mode in my case. It's iniitalization didn't happen before my path initialization so it's unusable.  
As `equal_string_ordinal_ic` is not used outside the `make_permissions` function and is set only once, I simply made it a static local.  
  
This works in my application (after recompiling everything).  
  
Some additional info: I built everything with VS2017.3.

---

## Comment 1

> Username: pdimov  
> Created_at: 2018-01-04 13:48:26 UTC  
> Url: https://github.com/boostorg/filesystem/pull/50#issuecomment-355286260  

Superseded by #62.

---
