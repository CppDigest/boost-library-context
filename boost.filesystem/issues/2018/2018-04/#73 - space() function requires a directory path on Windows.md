# #73 - space() function requires a directory path on Windows [Closed]

> Username: pchapmanvc  
> Created at: 2018-04-25 10:11:18 UTC  
> Updated at: 2020-05-04 22:18:26 UTC  
> Closed at: 2020-05-04 22:18:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/73  

I'm not sure whether this is a bug or a documentation issue.  
  
On Windows, the filesystem::space call passes its parameter to GetDiskFreeSpaceEx, which is documented as [requiring a directory](https://msdn.microsoft.com/en-us/library/windows/desktop/aa364937(v=vs.85).aspx).  
  
The boost documentation makes no such requirement, and just talks about posix fstatvfs, which will accept a file.  
  
So I think either the documentation needs updating to require a directory path, or the Windows code path needs to detect whether the parameter is a file and if so, strip the file name before calling GetDiskFreeSpaceEx.
