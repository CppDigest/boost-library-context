# #262 - boost::filesystem::is_normal_file() returns false for deduplicated files on Windows [Closed]

> Username: mtsart  
> Created at: 2022-10-14 18:07:13 UTC  
> Updated at: 2023-06-06 07:39:44 UTC  
> Closed at: 2022-12-03 07:37:16 UTC  
> Url: https://github.com/boostorg/filesystem/issues/262  

Checked on Boost versions 1.79, 1.80, with both NTFS and ReFS, Windows 10  
How to reproduce:  
- enable data deduplication on an NTFS or ReFS volume  
- make sure that there are some identical files. By default data deduplication is applied to files bigger than 32 kilobytes, created 3 days ago or earlier, if there are enough (100+ is definitely enough) copies.  
- run the optimization job (Start-DedupJob -Type Optimization -Volume X:) and wait for it to complete  
- check the attributes of the files that were intended to be deduplicated. If the optimization job has done what it should, they will have the L attribute on them in addition to the default A (Properties - Details - Attributes)  
- call boost::filesystem::is_normal_file() against such file, the return value will be false  
  
I believe it to be a bug due to those reasons:  
- files can be marked with this attribute out of the blue, with no user interaction, just because the OS deduplication code decided so  
- std::filesystem::is_normal_file() (MSVC runtime library) returns true for them  
  
Additional info: GetFileAttributesEx() gives dwFileAttributes==1568 (FILE_ATTRIBUTE_REPARSE_POINT | FILE_ATTRIBUTE_SPARSE_FILE | FILE_ATTRIBUTE_ARCHIVE) after the files were deduplicated and 32 (FILE_ATTRIBUTE_ARCHIVE) before that.

---

## Comment 1

> Username: Lastique  
> Created at: 2022-11-16 09:46:18 UTC  
> Url: https://github.com/boostorg/filesystem/issues/262#issuecomment-1316701851  

If the file is a reparse point, it is not a normal file. Boos.Filesystem uses `file_type::reparse_file` for such files.

---

## Comment 2

> Username: Lastique  
> Created at: 2022-11-16 10:47:37 UTC  
> Url: https://github.com/boostorg/filesystem/issues/262#issuecomment-1316791155  

On the second thought, it might make sense to add another exception just for dedup reparse tags. We already have an exception for mount points.

---

## Comment 3

> Username: paulharris  
> Created at: 2023-06-06 07:39:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/262#issuecomment-1578097489  

Thank you for this!  I have suffered from the reparse_point != regular_file since 2015...  
MS Windows Server 2012 was using it for deduplification,  
and Symantec Enterprise Vault (version 10) used reparse points for transparent archival to a secondary server.
