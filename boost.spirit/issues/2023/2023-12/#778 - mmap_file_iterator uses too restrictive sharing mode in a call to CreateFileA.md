# #778 - mmap_file_iterator uses too restrictive sharing mode in a call to CreateFileA [Open]

> Username: mikekaganski  
> Created at: 2023-12-01 16:54:54 UTC  
> Updated at: 2025-05-09 22:35:48 UTC  
> Url: https://github.com/boostorg/spirit/issues/778  

https://github.com/boostorg/spirit/blob/59515f0e56aebdf958eadab30be99cac8872e723/include/boost/spirit/home/classic/iterator/impl/file_iterator.ipp#L183  
  
`CreateFileA` call uses `FILE_SHARE_READ` as sharing mode. This makes this call fail, when the file is already open for writing, as explained at https://learn.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-createfilea#parameters (see `dwShareMode`).  
  
To allow opening files for reading, even when another process has it open for writing, but explicitly allowed reading using its own `FILE_SHARE_READ`, this call needs to use `FILE_SHARE_READ | FILE_SHARE_WRITE` (and maybe even also `| FILE_SHARE_DELETE`).  
  
https://bugs.documentfoundation.org/show_bug.cgi?id=158442 was caused by this problem (fixed by patching spirit).
