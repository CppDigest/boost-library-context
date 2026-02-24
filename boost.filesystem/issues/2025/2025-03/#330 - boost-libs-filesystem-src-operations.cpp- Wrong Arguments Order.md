# #330 - boost/libs/filesystem/src/operations.cpp: Wrong Arguments Order [Closed]

> Username: stasos24  
> Created at: 2025-03-04 10:29:13 UTC  
> Updated at: 2025-03-04 10:46:25 UTC  
> Closed at: 2025-03-04 10:46:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/330  

since at unistd.h:  
```  
extern int symlink (const char *__from, const char *__to)  
```  
  
there could be possible wrong arguments order at   
https://github.com/boostorg/filesystem/blob/30b312e5c0335831af61ad16802e888f5fb344ea/src/operations.cpp#L3511-L3517

---

## Comment 1

> Username: Lastique  
> Created at: 2025-03-04 10:46:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/330#issuecomment-2697053330  

[symlink(2)](https://man7.org/linux/man-pages/man2/symlink.2.html) documents the first argument of `symlink` as the target path and the second argument as the path of the symlink to be created. This matches the order of arguments of [`filesystem::create_directory_symlink`](https://www.boost.org/doc/libs/1_87_0/libs/filesystem/doc/reference.html#create_directory_symlink).
