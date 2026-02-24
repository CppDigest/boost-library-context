# #290 - Regression: can't stat files on Windows if file is open from another process [Closed]

> Username: toh-ableton  
> Created at: 2023-07-12 12:57:15 UTC  
> Updated at: 2023-07-13 10:10:34 UTC  
> Closed at: 2023-07-12 21:03:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/290  

If on Windows, one process has an open file handle to a file without `FILE_SHARE_READ`, trying to create a handle with `GENERIC_READ` in another process fails.  
  
This means that `creation_time`, `last_write_time` fail with `ERROR_SHARING_VIOLATION`.  
  
This regression was introduced in 1db4474d1ae7512af06f63156352f2ca5db07e35 with this comment in the commit message:  
```  
Also, use GENERIC_READ access mode when we call GetFileTime on the handle  
afterwards. GetFileTime documentation explicitly mentions that GENERIC_READ  
is required for it.  
```  
While it is true that the `GetFileTime` documentation mentions this, this is obviously a bug in the documentation. Until [boost 1.79](https://github.com/boostorg/filesystem/commit/d732ab006ab133b40549edd6d66c9597c36fb4d1), a value of `0` was passed for the `dwDesiredAccess` parameter, which apparently worked just fine and is also documented to work in the [CreateFileW documentation](https://learn.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-createfilew):  
```  
If this parameter is zero, the application can query certain metadata such as file, directory, or device attributes without accessing that file or device, even if GENERIC_READ access would have been denied.  
```  
  
To fix this, the `dwDesiredAccess` parameter changes in `creation_time` and `last_write_time` need to be reverted, either to `FILE_READ_ATTRIBUTES` or to `0`.

---

## Comment 1

> Username: Lastique  
> Created at: 2023-07-12 15:16:32 UTC  
> Url: https://github.com/boostorg/filesystem/issues/290#issuecomment-1632734320  

The thing is apparently these access modes are handled separately by each and every filesystem. What works on NTFS may not work on SMB or FAT or any other third party filesystem driver. The commit you referenced works around precisely such discrepancy in behavior. In a mess like this, at least following the documentation is the most reasonable way to go.  
  
Re. `CreateFileW`, it only mentions [file attributes](https://learn.microsoft.com/en-us/windows/win32/fileio/file-attribute-constants). I do not think file times are considered as attributes.

---

## Comment 2

> Username: toh-ableton  
> Created at: 2023-07-13 10:10:34 UTC  
> Url: https://github.com/boostorg/filesystem/issues/290#issuecomment-1633954784  

@Lastique thanks for the quick fix! 💐
