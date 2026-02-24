# #166 - Bad error handling in shared_memory_object::truncate [Closed]

> Username: andrust  
> Created at: 2022-01-06 14:29:42 UTC  
> Updated at: 2022-03-07 13:59:07 UTC  
> Closed at: 2022-03-07 12:55:07 UTC  
> Url: https://github.com/boostorg/interprocess/issues/166  

https://github.com/boostorg/interprocess/blob/6982a86d97edfffb0f69230c3ca5f7ed9a1a1200/include/boost/interprocess/shared_memory_object.hpp#L466  
  
According to posix_fallocate man page: _posix_fallocate() returns zero on success, or an error number on failure.  Note that errno is not set._  
  
`ret` should be used instead of `system_error_code()` when throwing exception.

---

## Comment 1

> Username: igaztanaga  
> Created at: 2022-03-07 12:40:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/166#issuecomment-1060645735  

Thanks for the report, I'll apply the fix you propose.

---

## Comment 2

> Username: andrust  
> Created at: 2022-03-07 13:59:07 UTC  
> Url: https://github.com/boostorg/interprocess/issues/166#issuecomment-1060716342  

Thanks for fixing!
