# #246 Limit the buffer size for compatibility with previous versions of Windows [Closed]

> Username: obelloc  
> Created at: 2022-08-08 09:39:55 UTC  
> Updated at: 2022-08-08 14:23:26 UTC  
> Closed at: 2022-08-08 14:22:35 UTC  
> Url: https://github.com/boostorg/filesystem/pull/246  

Windows API functions NtQueryDirectoryFile and GetFileInformationByHandleEx can fail with ERROR_INVALID_PARAMETER if the provided buffer is greater than 64k, and the file handle refers to a directory located on a network share, and the shared folder is itself in the local computer. This was reproduced on Windows 7 and 8.1, but *not* on a early release of Windows 10 (v2004).  
  
I could not find any documentation regarding this issue, apart from a brief comment on another open-source project:  
[https://github.com/FarGroup/FarManager/blob/master/far/platform.fs.cpp#L620](https://github.com/FarGroup/FarManager/blob/master/far/platform.fs.cpp#L620)  
  
The argument to have a buffer greater than 64k is based on the claim that ReFS supports filenames up to 32768 UTF-16 characters, but I could not find any documentation supporting this claim. In contrast, I found this Microsoft   
[documentation](https://docs.microsoft.com/en-us/windows-server/storage/refs/refs-overview#limits) that mentions the maximum filename length to be 255 for both NTFS and ReFS.  
  
Therefore, I believe 64k should be enough.

---

## Comment 1

> Username: Lastique  
> Created_at: 2022-08-08 10:46:06 UTC  
> Url: https://github.com/boostorg/filesystem/pull/246#issuecomment-1207961844  

The filename limit of 32768 characters is mentioned [here](https://www.howtogeek.com/311840/what-is-refs-the-resilient-file-system-on-windows/) and also somewhat confusingly [here](https://docs.microsoft.com/en-us/archive/blogs/b8/building-the-next-generation-file-system-for-windows-refs). The last link says both 32K and 255, and 255 seems to be the limit inherited from the implementation bits shared with NTFS. I haven't found the description of the on-disk format to see the actual limit of the format, so I'm guessing the on-disk format allows 32K but the implementation and API allows 255 characters. If this is the case, limiting the buffer might prohibit some very long filenames on Windows versions that do actually support the 32K limit.

---

## Comment 2

> Username: obelloc  
> Created_at: 2022-08-08 12:09:47 UTC  
> Url: https://github.com/boostorg/filesystem/pull/246#issuecomment-1208044300  

I also cannot tell for sure.   
  
But, the first link you mentioned does not look very "precise" in it's terms. The mentioned paragraph seems to be talking about path limits, not file names. It starts by saying "On an NTFS file system, file **paths** are limited to 255 characters..." and then draws a comparison with ReFS, and then finishes by providing a link on how to remove the **path** limit on Windows 10. So, it really seems to me that the term "file name", when referring to ReFS in this context, refers to something like a complete file name, including the path itself. Otherwise, I guess the comparison does not make much sense - but this is just my 2c.  
  
Regarding the second link, it cannot be used as an argument either way. =)  To be honest, it's indeed interesting the sentence "for compatibility this was made consistent with NTFS for the RTM product", which seems to indicate that they had deliberately limited the filename to 255 for the ReFS product release.  
  
So, I would honestly consider this documentation [link](https://docs.microsoft.com/en-us/windows-server/storage/refs/refs-overview#limits) as good of an evidence as the other ones.  
  
I would also say that the current implementation breaks on somewhat common use cases, like accessing network shares on Windows 7/8.1, in exchange of supporting a somewhat obscure use case (which might not even exist). For instance, we already have a reproducible issue on Windows 7/8.1 - we could fix that, and then wait until we are sure there is really an issue with ReFS (like another pull request/bug report), instead of doing it the other way around..  
  
I hope I am making sense, thx.

---

## Comment 3

> Username: Lastique  
> Created_at: 2022-08-08 14:23:25 UTC  
> Url: https://github.com/boostorg/filesystem/pull/246#issuecomment-1208198284  

Thank you for the contribution. I've committed a slightly updated version of the fix (changed comment and added a release note).

---
