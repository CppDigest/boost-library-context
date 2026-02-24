# #154 - shared_memory_object::truncate() fails on AIX [Closed]

> Username: kalikin  
> Created at: 2021-09-22 19:14:53 UTC  
> Updated at: 2021-09-24 17:05:22 UTC  
> Closed at: 2021-09-24 17:04:34 UTC  
> Url: https://github.com/boostorg/interprocess/issues/154  

Hi. I just tried a small example on AIX and found that shared_memory_object::truncate() always fails:  
```cpp  
shared_memory_object shdmem(open_or_create, "test", read_write);  
shdmem.truncate(1024);  
```  
It looks like [truncate()](https://github.com/boostorg/interprocess/blob/e4259fe16804f5a4b179e1c7e69d201bd532d31a/include/boost/interprocess/shared_memory_object.hpp#L457) actually makes these two calls: `posix_fallocate` and `ftruncate`. And `posix_fallocate`, which was introduced in #106, returns `ENODEV` error code, which means "The fd parameter does not refer to a regular file", according to [docs](https://www.ibm.com/docs/en/aix/7.2?topic=p-posix-fallocate-subroutine).

---

## Comment 1

> Username: igaztanaga  
> Created at: 2021-09-24 17:05:22 UTC  
> Url: https://github.com/boostorg/interprocess/issues/154#issuecomment-926786732  

Thanks for the report. Let me know if the commit fixes the problem in AIX, I have no access to that OS
