# #120 - C++ exception with description "boost::filesystem::resize_file: File too large [Closed]

> Username: xgdgsc  
> Created at: 2019-09-24 08:12:30 UTC  
> Updated at: 2020-03-04 13:33:32 UTC  
> Closed at: 2019-10-05 16:29:10 UTC  
> Url: https://github.com/boostorg/filesystem/issues/120  

What' s the limit of file resize? I want to resize to 17GB  on a 64bit ubuntu system.

---

## Comment 1

> Username: Lastique  
> Created at: 2019-10-05 16:29:10 UTC  
> Url: https://github.com/boostorg/filesystem/issues/120#issuecomment-538665170  

The limit is the max value of `off_t`, as mandated by `truncate` system call. `off_t` is a signed 64-bit integer on 64-bit Linux.  
  
The error you're getting might not be from Boost.Filesystem, but from your OS. You should debug to know for sure.

---

## Comment 2

> Username: xgdgsc  
> Created at: 2020-03-04 13:33:32 UTC  
> Url: https://github.com/boostorg/filesystem/issues/120#issuecomment-594524679  

Thanks. Turns out to be an issue of integer multiplication assigned to unsigned integer causing too large file size.
