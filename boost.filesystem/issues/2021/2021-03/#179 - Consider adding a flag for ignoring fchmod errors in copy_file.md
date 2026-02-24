# #179 - Consider adding a flag for ignoring fchmod errors in copy_file [Closed]

> Username: Lastique  
> Created at: 2021-03-20 17:46:22 UTC  
> Updated at: 2024-01-13 14:43:25 UTC  
> Closed at: 2024-01-13 14:43:25 UTC  
> Url: https://github.com/boostorg/filesystem/issues/179  

As described in https://github.com/boostorg/filesystem/issues/178, there are cases when `fchmod` consistently fails for a particular filesystem. This causes `copy_file` failure, although the file is successfully copied, though without setting the original access mode and permissions. It may be desirable to suppress the error from `copy_file` if `fchmod` fails.  
  
One possible solution is to add a new flag in `copy_options` to ignore errors from `fchmod`.  
  
Side note: It may be worthwhile to detect NFS with [`statfs`](https://man7.org/linux/man-pages/man2/statfs.2.html) (for it `f_type` should be `NFS_SUPER_MAGIC`) and only set `S_IWUSR` permission when the target file is on NFS. This way we may not have to change the target file permissions after copying. Although, if a filesystem does not support `fchmod`, it is possible that `fstat`/`statx` for the target file returns a different mode from the original file, which means we will try calling `fchmod` anyway.

---

## Comment 1

> Username: kkumar45  
> Created at: 2022-10-12 12:04:56 UTC  
> Url: https://github.com/boostorg/filesystem/issues/179#issuecomment-1276050484  

@Lastique: Any update on it, when it will be planned?

---

## Comment 2

> Username: Lastique  
> Created at: 2022-10-12 17:00:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/179#issuecomment-1276477918  

No updates, unfortunately. I haven't got around to it.

---

## Comment 3

> Username: kkumar45  
> Created at: 2023-09-26 09:00:32 UTC  
> Url: https://github.com/boostorg/filesystem/issues/179#issuecomment-1735122606  

@Lastique Can you please consider it? Our customers are impacted due to this.
