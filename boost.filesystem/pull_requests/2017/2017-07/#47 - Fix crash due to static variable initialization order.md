# #47 Fix crash due to static variable initialization order [Closed]

> Username: rcdailey  
> Created at: 2017-07-24 19:09:37 UTC  
> Updated at: 2017-08-08 17:48:08 UTC  
> Closed at: 2017-08-08 17:48:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/47  

The static variable `equal_string_ordinal_ic` is not yet initialized when  
invoking `boost::filesystem::remove_all(dir)` also at static initialization  
time.  
  
Real example code that causes the issue (global scope):  
  
```  
/// \hack  
/// Forces 1 time shared memory cleanup on filesystem.  
static class cleanup_shared_memory  
{  
public:  
   cleanup_shared_memory()  
   {  
      try  
      {  
         std::string dir;  
         ipcdetail::get_shared_dir_root(dir);  
         boost::filesystem::remove_all(dir);  
         LOG_INFO << "Removed old shared memory data: " << dir;  
      }  
      catch (boost::filesystem::filesystem_error const& e)  
      {  
         LOG_ERROR << "Could not remove old shared memory data: " << e.what();  
      }  
   }  
} do_not_remove;  
```

---

## Review 1 [Commented]

> Username: Lastique  
> Created_at: 2017-08-08 16:41:53 UTC  
> State: COMMENTED  
> Url: https://github.com/boostorg/filesystem/pull/47#pullrequestreview-54998081  

📁 src/operations.cpp

```diff
 617 |   perms make_permissions(const path& p, DWORD attr)
 618 |   {
 619 |+     static Ptr_equal_string_ordinal_ic equal_string_ordinal_ic =
```

> Username: Lastique  
> Created_at: 2017-08-08 16:41:53 UTC  
> Url: https://github.com/boostorg/filesystem/pull/47#discussion_r131966787  

Function-local statics are not thread-safe in C++03.

> Username: rcdailey  
> Created_at: 2017-08-08 17:43:20 UTC  
> Updated_at: 2017-08-08 17:43:45 UTC  
> Url: https://github.com/boostorg/filesystem/pull/47#discussion_r131982843  

Statics aren't thread safe *period*. I expect to see some synchronization happening here if this needs to be thread safe. I don't see how my change affects the threading aspect of the code, since a global isn't thread safe either.


---

## Comment 2

> Username: rcdailey  
> Created_at: 2017-08-08 17:48:08 UTC  
> Url: https://github.com/boostorg/filesystem/pull/47#issuecomment-321031053  

Closing in favor of #43 (mine is a duplicate of that and more discussion has happened there).

---
