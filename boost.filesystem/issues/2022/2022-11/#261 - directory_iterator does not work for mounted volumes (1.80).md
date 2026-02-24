# #261 - directory_iterator does not work for mounted volumes (1.80) [Closed]

> Username: basiliscos  
> Created at: 2022-11-14 09:43:00 UTC  
> Updated at: 2022-12-21 13:13:00 UTC  
> Closed at: 2022-12-02 17:36:16 UTC  
> Url: https://github.com/boostorg/filesystem/issues/261  

`Z:\` is mounted volume, while `C:\` is native. The `directory_iterator("Z:\\") == directory_iterator()` is true, while for `C:\` it is false

---

## Comment 1

> Username: Lastique  
> Created at: 2022-11-14 10:00:40 UTC  
> Updated at: 2022-11-14 10:00:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/261#issuecomment-1313404580  

I assume, `Z:\` is not empty (i.e.contains files and directories) and you have permissions to list them? Because otherwise your test results are as expected.  
  
I need more details and a way to reproduce this. What do you mean by "mounted volume" and how is Z different from C? Because every drive letter except those created with `subst` is a mounted volume. If you're using some non-standard filesystem, what is it?  
  
Also, did you try to debug this? Does some WinAPI call fail? Which one and with what error code?  
  
Also, what Windows are you running?

---

## Comment 2

> Username: basiliscos  
> Created at: 2022-11-15 07:17:23 UTC  
> Url: https://github.com/boostorg/filesystem/issues/261#issuecomment-1314876591  

Hi.   
  
Here are the relevant screenshots, with which you can get the requested information, at least partly. I'm mostly Linux user, so, a bit not too comfortant with Windows, that's why I used term "mounted" volumes; in the Virtual Box the term "shared folder" is used.  
  
I don't know which syscall fails, because I seed some mangled function names in sources instead of win32-api functions.   
  
In the last screenshot with `cmd.exe` you can see the execution result of `tut3.cpp` from the shipped examples: it works fine for `c:\`:, while it has no output on `z:\`, although everything is fine with `z:\`, because I can even build whole project with `cmake` there. I assume, the similar problems with boost-filesystem and directory iterator will be on other "network shares" on windows.  
  
  
![2022-11-15_09-52](https://user-images.githubusercontent.com/3630048/201853007-3aab413d-e27f-4736-928b-eaa1050492e3.png)  
![2022-11-15_09-55](https://user-images.githubusercontent.com/3630048/201853009-501c9a1a-32d9-42dc-8a78-e38149809e8f.png)  
![2022-11-15_09-58](https://user-images.githubusercontent.com/3630048/201853013-b8150329-d727-45b9-a257-d0cd88e8ff9c.png)  
![2022-11-15_10-08](https://user-images.githubusercontent.com/3630048/201853015-bcc08064-4349-4d15-be5a-c5f3f4a939bb.png)  
  
Thank you for helping.

---

## Comment 3

> Username: Lastique  
> Created at: 2022-12-02 17:36:16 UTC  
> Updated at: 2022-12-02 17:38:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/261#issuecomment-1335574338  

Mounted network shares work for me.  
  
This seems to be a bug in the VBoxSharedFolderFS (as indicated in the mount point properties). `GetFileInformationByHandleEx(FileFullDirectoryRestartInfo)` fails with `ERROR_FILE_NOT_FOUND` [here](https://github.com/boostorg/filesystem/blob/8b71cb11a3c60e34ae8f4a52ac44b1d5fc93d0b0/src/directory.cpp#L857), which is a sign that the directory is empty. Hence the returned directory iterator is an end iterator.  
  
Using `std::filesystem` from VS2022 instead of Boost.Filesystem also doesn't quite work: it shows only two files in the shared folder, although there are dozens of files and directories in it. They are [using](https://github.com/microsoft/STL/blob/e28f9561233a58d48d893094ed3a6bc0c5ee6ad9/stl/src/filesystem.cpp#L246-L256) `FindFirstFileExW` internally, which is likely using the same basic primitives as those `GetFileInformationByHandleEx` queries (I suspect `NtQueryDirectoryFile`). I'm not sure why it shows only two files instead of none.  
  
This should be [reported](https://www.virtualbox.org/wiki/Bugtracker) to VirtualBox.  
  
As a workaround, you can use network shares instead of VirtualBox shared folders.

---

## Comment 4

> Username: basiliscos  
> Created at: 2022-12-21 13:13:00 UTC  
> Url: https://github.com/boostorg/filesystem/issues/261#issuecomment-1361297527  

I created an issue for VB, https://www.virtualbox.org/ticket/21351
