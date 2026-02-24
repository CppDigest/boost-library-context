# #10 - aligned_alloc uses posix_memalign on MinGW [Closed]

> Username: Lastique  
> Created at: 2020-02-25 16:47:48 UTC  
> Updated at: 2020-02-29 21:49:13 UTC  
> Closed at: 2020-02-29 21:49:12 UTC  
> Url: https://github.com/boostorg/align/issues/10  

Legacy MinGW targets native Windows and does not provide `posix_memalign`.  
  
Discovered by Boost.Atomic test [failures](https://ci.appveyor.com/project/Lastique/atomic/builds/31044903/job/lx00sgydy86nj0nv#L597).

---

## Comment 1

> Username: glenfe  
> Created at: 2020-02-25 17:26:16 UTC  
> Url: https://github.com/boostorg/align/issues/10#issuecomment-590976213  

Thanks, I'll take care of this.

---

## Comment 2

> Username: glenfe  
> Created at: 2020-02-29 21:49:12 UTC  
> Url: https://github.com/boostorg/align/issues/10#issuecomment-592998089  

Should be resolved in b8e81e1.
