# #282 - filesystem::is_directory() returns wrong result for directories on smb 1 network shares [Closed]

> Username: boytsovea  
> Created at: 2023-03-28 11:49:54 UTC  
> Updated at: 2023-04-12 22:45:36 UTC  
> Closed at: 2023-04-01 10:23:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282  

We have switched our codebase from boost 1.61 to 1.80 and observed wrong behaviour of function is_directory(p) for some directories located on some network shares. Namely, this function returns that filesystem object is not a directory even in reality it is. After some investigations we were able to reproduce the issue on a virtual machine, which runs smb version 1 network share. The following code (here "\\WIN-ZL5F8K2HI90\Users\Default" is beforementioned smb1 network share on a virtual machine):  
  
```  
#include <boost/filesystem.hpp>  
#include <iostream>  
#include <fileapi.h>  
  
int main()  
{  
   bool res = boost::filesystem::is_directory( L"\\\\WIN-ZL5F8K2HI90\\Users\\Default" );  
   std::cout << res << std::endl;  
   DWORD win_api_res = GetFileAttributesW( L"\\\\WIN-ZL5F8K2HI90\\Users\\Default" );  
   std::cout << win_api_res << std::endl;  
   return 0;  
}  
```  
  
produces the following output:  
  
0 // that is boost::filesystems thinks that "\\WIN-ZL5F8K2HI90\Users\Default" is not a directory  
19 // that is GetFileAttributesW states that  "\\WIN-ZL5F8K2HI90\Users\Default" is a directory  
  
We believe that the root cause of the issue is commit https://github.com/boostorg/filesystem/commit/97722a3107d136eebe9425804fe0d13eab3ce2bc

---

## Comment 1

> Username: Lastique  
> Created at: 2023-03-28 12:12:43 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1486768690  

Please, retest with the latest version from git develop or Boost 1.82 beta1.

---

## Comment 2

> Username: boytsovea  
> Created at: 2023-03-29 07:32:52 UTC  
> Updated at: 2023-03-29 07:35:02 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1488085733  

With the latest version of boost the problem still persists. We've debugged library source code in operations.cpp for our example - it follows "normal" execution path. The function GetFileInformationByHandleEx just returns, that problematic directory is a file.

---

## Comment 3

> Username: Lastique  
> Created at: 2023-03-29 10:51:41 UTC  
> Updated at: 2023-03-29 10:59:01 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1488372397  

Well, in that case I'm not sure what I can do. What Windows version are you using? Both client and server.

---

## Comment 4

> Username: Lastique  
> Created at: 2023-03-29 11:12:05 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1488401327  

Also, I noticed you explicitly mention SMBv1. Note that this protocol is [deprecated and not installed by default](https://learn.microsoft.com/en-us/windows-server/storage/file-server/troubleshoot/smbv1-not-installed-by-default-in-windows). Does this reproduce with SMBv2 or SMBv3?

---

## Comment 5

> Username: boytsovea  
> Created at: 2023-03-29 14:28:04 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1488733932  

We've used win 10 as a client and win server 2008 as a server in our experiment. We were unable to reproduce the issue with SMBv2 or SMBv3 in our setup, but we don't know exactly whether these results are reliable since we don't know the underlying nature of the problem at the OS level.  
  
We know, that SMB v1 is deprecated, but many users have legacy network infrastructure with only SMB v1 in use. For GetFileInformationByHandleEx and GetFileInformationByHandle Microsoft explicitly states in their documentation that   
  
> Depending on the underlying network features of the operating system and the type of server connected to, the GetFileInformationByHandle function may fail, return partial information, or full information for the given file.   
  
With old GetFileAttributesW-based implementation the issue doesn't arise. Also, std::filesystem works a expected in this case.

---

## Comment 6

> Username: Lastique  
> Created at: 2023-03-29 16:33:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1488931868  

Using `GetFileAttributesW` is prone to race conditions because this system call alone is not sufficient for implementing `symlink_status` completely - we need to inspect the reparse tag for reparse points, which requires opening the file and calling `GetFileInformationByHandleEx` anyway. There is a time window between `GetFileAttributesW` and opening the file, when the file at the given path may be replaced, which would produce inconsistent results.  
  
Therefore we need to query file attributes through file handles, at least in the majority of cases where this is possible. I would consider adding a workaround for SMBv1 - if we can detect this particular case. I'm unlikely to be able to set up a test system any time soon, so I would appreciate if you could help with this.  
  
Here are a few ideas to test:  
  
- When `GetFileInformationByHandleEx` returns file attributes, e.g. [here](https://github.com/boostorg/filesystem/blob/7745d57ca8dccd394cdf1c33bea76589d592ec60/src/operations.cpp#L1612), are these attributes in any way unusual in your case? For example, are the attributes 0? For regular files, I would expect at least `FILE_ATTRIBUTE_NORMAL` to be set. Please compare the attributes you get for a SMBv1 directory with attributes you get for files and directories on your local filesystem and with SMBv2/v3.  
- Do other types of file information work for the SMBv1 directory? For example, `GetFileInformationByHandleEx(FileStandardInfo)` returns [`FILE_STANDARD_INFO`](https://learn.microsoft.com/en-us/windows/win32/api/winbase/ns-winbase-file_standard_info) - is `FILE_STANDARD_INFO::Directory` also `false` in your case?  
- Does [`GetVolumeInformationByHandleW`](https://learn.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-getvolumeinformationbyhandlew) work for your SMBv1 directory? If yes, what does it return in `lpFileSystemNameBuffer`? Also check if the returned string is different from SMBv2 and SMBv3.

---

## Comment 7

> Username: boytsovea  
> Created at: 2023-03-31 07:37:07 UTC  
> Updated at: 2023-03-31 07:52:20 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1491449489  

We've tried to test GetFileInformationByHandleEx with our setup and accidentally discovered, that changing dwDesiredAccess to GENERIC_READ or FILE_READ_EA in call to CreateFile solves the issue (but i don't know whether such change can be safely applied).  
  
So that code (like in current boost):  
  
`HANDLE file_handle = create_file_handle( L"\\\\WIN-ZL5F8K2HI90\\Users\\test", FILE_READ_ATTRIBUTES, FILE_SHARE_READ | FILE_SHARE_WRITE | FILE_SHARE_DELETE,  
                                           nullptr, OPEN_EXISTING, FILE_FLAG_BACKUP_SEMANTICS | FILE_FLAG_OPEN_REPARSE_POINT );`  
`FILE_ATTRIBUTE_TAG_INFO info;`  
`GetFileInformationByHandleEx( file_handle, FileAttributeTagInfo, &info, sizeof(info) );`  
  
gives 128 (i.e. "FILE_ATTRIBUTE_NORMAL")  
  
And that code:  
  
`HANDLE file_handle = create_file_handle( L"\\\\WIN-ZL5F8K2HI90\\Users\\test", GENERIC_READ, FILE_SHARE_READ | FILE_SHARE_WRITE | FILE_SHARE_DELETE,  
                                           nullptr, OPEN_EXISTING, FILE_FLAG_BACKUP_SEMANTICS | FILE_FLAG_OPEN_REPARSE_POINT );`  
`FILE_ATTRIBUTE_TAG_INFO info;`  
`GetFileInformationByHandleEx( file_handle, FileAttributeTagInfo, &info, sizeof(info) );`  
  
gives 16 (i.e. "FILE_ATTRIBUTE_DIRECTORY")

---

## Comment 8

> Username: boytsovea  
> Created at: 2023-03-31 07:49:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1491464381  

Call to GetFileInformationByHandleEx for FileStandardInfo also reports that filesystem object is a directory (in info.Directory field of FILE_STANDARD_INFO) even with dwDesiredAccess=FILE_READ_ATTRIBUTES in CreateFile:  
  
```  
HANDLE file_handle = create_file_handle( L"\\\\WIN-ZL5F8K2HI90\\Users\\test", FILE_READ_ATTRIBUTES, FILE_SHARE_READ | FILE_SHARE_WRITE | FILE_SHARE_DELETE,  
                                           nullptr, OPEN_EXISTING, FILE_FLAG_BACKUP_SEMANTICS | FILE_FLAG_OPEN_REPARSE_POINT );  
  
FILE_STANDARD_INFO info;  
GetFileInformationByHandleEx( file_handle, FileStandardInfo, &info, sizeof(info) );  
```

---

## Comment 9

> Username: Lastique  
> Created at: 2023-03-31 11:55:21 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1491815034  

Thanks for all the information.  
  
I have implemented a fix in branch `feature/win_fix_smb1`, could you test if commit https://github.com/boostorg/filesystem/commit/1db4474d1ae7512af06f63156352f2ca5db07e35 fixes the issue for you?  
  
It would be great if you could run library tests in an SMBv1 share, or at least check if other file operations, such as `remove`, `remove_all`, `copy_file`, work with files in an SMBv1 share. If you want to run library tests, build them first by running `b2` in `libs/fileystem/test` (or `b2 libs/fileystem/test` from Boost root), then `cd` to a writable test directory in the SMBv1 share, then run the tests again using the full path to test executables. The executables will be inside the `<Boost root>/bin.v2/libs/filesystem` directory tree. I'm most interested in `operations_test`.

---

## Comment 10

> Username: Lastique  
> Created at: 2023-04-01 10:25:26 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1492917897  

The fix is now merged into develop. Thanks again for all the testing. If you can, please still test that your usecase works with the fixed Boost.Filesystem.

---

## Comment 11

> Username: boytsovea  
> Created at: 2023-04-04 08:43:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1495581262  

This patch solves the problem in our setup. Thank you!

---

## Comment 12

> Username: cdecker08  
> Created at: 2023-04-12 19:38:50 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1505822601  

I believe directory_iterators are still broken with smbv1. Should I file another issue?

---

## Comment 13

> Username: Lastique  
> Created at: 2023-04-12 22:45:35 UTC  
> Url: https://github.com/boostorg/filesystem/issues/282#issuecomment-1506062555  

Yes, and I would appreciate the detailed info on what system call is failing and how.
