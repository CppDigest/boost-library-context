# #195 - windows_file_codecvt class is not inside boost:: namespace [Closed]

> Username: naelolaiz  
> Created at: 2021-06-11 10:45:15 UTC  
> Updated at: 2021-06-11 13:07:10 UTC  
> Closed at: 2021-06-11 12:12:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/195  

When doing a windows build, windows_file_codecvt class is included in path.cpp, which is in the global namespace.  
This is a special problem when using a boost custom namespace to avoid symbols collision when linking with different boost versions, since windows_file_codecvt gets exported unprefixed.

---

## Comment 1

> Username: Lastique  
> Created at: 2021-06-11 11:20:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/195#issuecomment-859511506  

Thanks for the report.  
  
Linking multiple Boost versions is not supported and may fail in subtle ways. But moving `windows_file_codecvt` into a namespace makes sense.

---

## Comment 2

> Username: naelolaiz  
> Created at: 2021-06-11 13:07:10 UTC  
> Url: https://github.com/boostorg/filesystem/issues/195#issuecomment-859569088  

Wow, that was fast!   
Thank you!
