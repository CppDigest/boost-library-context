# #309 - Boost winapi file_management dwReserved bug? [Closed]

> Username: hoyhoy  
> Created at: 2024-05-19 20:43:47 UTC  
> Updated at: 2024-05-19 23:24:30 UTC  
> Closed at: 2024-05-19 21:19:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/309  

Detected by msvc /analyze.  
  
```  
boost\p\include\boost/winapi/file_management.hpp(494,1): error C2220: the following warning is treated as an error  
boost\p\include\boost\winapi\file_management.hpp(493): warning C6388: 'dwReserved' might not be '0':  this does not adhere to the specification for the function 'LockFileEx'  
```

---

## Comment 1

> Username: Lastique  
> Created at: 2024-05-19 21:19:22 UTC  
> Url: https://github.com/boostorg/filesystem/issues/309#issuecomment-2119363351  

1. Boost.Filesystem does not call `LockFileEx`.  
2. Boost.WinAPI only forwards arguments to `LockFileEx` that were passed to it by the caller. If the caller passes invalid arguments, that's a problem in the caller.  
3. If there is no caller, and MSVC simply generates a warning solely based on the definition of the [`LockFileEx`](https://github.com/boostorg/winapi/blob/39396bd78254053f3137510478e8f956bd2b83d4/include/boost/winapi/file_management.hpp#L485-L494) inline function then the warning is bogus and I'd consider this a bug in the analyzer.

---

## Comment 2

> Username: hoyhoy  
> Created at: 2024-05-19 22:53:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/309#issuecomment-2119385713  

Wouldn't it be a whole lot easier if you just ignored in the boost headers rather than dumping the problem into user space?  Our code doesn't even call FileLock.  Just including the header causes the issue.

---

## Comment 3

> Username: Lastique  
> Created at: 2024-05-19 23:24:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/309#issuecomment-2119397302  

Please, bring your complaints to Microsoft.
