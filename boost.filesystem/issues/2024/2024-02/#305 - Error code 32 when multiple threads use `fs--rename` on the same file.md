# #305 - Error code 32 when multiple threads use `fs::rename` on the same file. [Closed]

> Username: tlehman  
> Created at: 2024-02-06 00:30:51 UTC  
> Updated at: 2024-02-06 01:51:24 UTC  
> Closed at: 2024-02-06 01:49:44 UTC  
> Url: https://github.com/boostorg/filesystem/issues/305  

I am working on a program called [pxp-agent](https://github.com/puppetlabs/pxp-agent/) which makes use of the [leatherman](https://github.com/puppetlabs/leatherman) library (uses Boost 1.73). I am running into an error when I compile this for windows.  
  
**The error**: when multiple tasks are queued up in pxp-agent and they attempt to download the same task in separate threads, [this line](https://github.com/puppetlabs/leatherman/blob/main/curl/src/client.cc#L138)'s call to `fs::rename` fails with `system:32`, which is an ERROR_SHARING_VIOLATION.  
  
[This SO question](https://stackoverflow.com/questions/30998668/movefileex-returns-error-sharing-violation) suggests that it might be a problem where one of the threads needs to call CloseHandle.  
  
When I build this code for POSIX systems, I don't run into this problem, so it's specific to Windows:  
  
[1_73_0/libs/filesystem/src/operations.cpp](https://www.boost.org/doc/libs/1_73_0/libs/filesystem/src/operations.cpp)  
```C++  
#   define BOOST_MOVE_FILE(OLD,NEW)(::MoveFileExW(OLD, NEW, MOVEFILE_REPLACE_EXISTING|MOVEFILE_COPY_ALLOWED)!= 0)  
```  
  
Is there some way I can configure Boost to fix this? Or is this a known issue that can be resolved by upgrading from Boost 1.73? Thank you.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-02-06 01:49:44 UTC  
> Updated at: 2024-02-06 01:51:24 UTC  
> Url: https://github.com/boostorg/filesystem/issues/305#issuecomment-1928636997  

If the file is opened without the necessary sharing permission (see [`CreateFile`](https://learn.microsoft.com/en-us/windows/win32/api/fileapi/nf-fileapi-createfilea) documentation for `dwShareMode`) then `MoveFileExW` will fail with `ERROR_SHARING_VIOLATION`. I'm guessing, at least `FILE_SHARE_READ | FILE_SHARE_DELETE` share mode must be used on every handle for the file for `MoveFileExW` to succeed (`FILE_SHARE_READ` would be necessary if `MoveFileExW` falls back to `CopyFile`+`DeleteFile` implementation).  
  
Boost.Filesystem doesn't keep file handles for files across function calls, other than directories that are used during directory iteration. Even then, the library code currently enables both `FILE_SHARE_READ` and `FILE_SHARE_DELETE` for handles it opens. So I strongly suspect that either your application opens a handle to the file without one of those sharing permissions, or there is another process in the system that does so. Note that this can truly be *any* process on the system, including e.g. antivirus software.  
  
I remember there were fixes in Boost.Filesystem regarding file sharing permissions some time ago. It is likely that that was after Boost 1.73. It's difficult to tell for sure as there were too many changes since that release. I recommend you try with the latest Boost release. However, the requirement that all other handles for the file must have the necessary sharing permissions is still in effect, and there's nothing I can do to circumvent it. That's just how Windows works.  
  
PS: The SO answer is only partially true. Sure, you can "fix" the problem by closing all other handles to the file. But the actual problem is the lack of the necessary sharing permissions on the handles. Without the permissions, the problem will still reproduce whenever the offending handle happens to exist while you're attempting to move the file.
