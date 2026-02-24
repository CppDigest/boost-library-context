# #168 - mingw32 fails to check symlink existence right after the creation [Closed]

> Username: apolukhin  
> Created at: 2020-12-16 09:24:29 UTC  
> Updated at: 2022-07-24 21:42:30 UTC  
> Closed at: 2022-07-24 21:41:12 UTC  
> Url: https://github.com/boostorg/filesystem/issues/168  

With the 1.75 release the issue https://svn.boost.org/trac10/ticket/13135 now can be reproduced on MinGW  
  
Here's a [CI Build](https://ci.appveyor.com/project/apolukhin/boost-cookbook/branch/second_edition/job/ienbu5xucngpbr4h) with the output:  
```  
Return code in "Chapter11/02_erasing_files" test is 3, 0 expected. Info:  
--- Stdout:  
--- Stderr:  
Symlink created  
This application has requested the Runtime to terminate it in an unusual way.  
Please contact the application's support team for more information.  
Assertion failed!  
Program: C:\projects\boost-cookbook\Chapter11\02_erasing_files\debug\02_erasing_files.exe  
File: main.cpp, Line 27  
Expression: boost::filesystem::exists("symlink")  
--- Ret code: 3  
```  
  
Link to the test https://github.com/apolukhin/Boost-Cookbook/blob/0f3374d6b95be9ee6015ad105c8f0a3ab7c2e29f/Chapter11/02_erasing_files/main.cpp#L27

---

## Comment 1

> Username: Lastique  
> Created at: 2022-07-24 21:41:12 UTC  
> Updated at: 2022-07-24 21:42:30 UTC  
> Url: https://github.com/boostorg/filesystem/issues/168#issuecomment-1193399132  

We are [using](https://github.com/boostorg/filesystem/blob/ea22e7655258b61776e4213ca4001a6b97b3541b/src/operations.cpp#L3326) [`CreateSymbolicLinkW`](https://docs.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-createsymboliclinkw) to create symlinks. In Boost.Filesystem tests, we [do verify](https://github.com/boostorg/filesystem/blob/ea22e7655258b61776e4213ca4001a6b97b3541b/test/operations_test.cpp#L849) that symlinks are created, so `create_symlink` works. If it doesn't get created immediately for some reason in your case then, I suppose, that's how Windows works and I don't see what I can do about it in Boost.Filesystem. Perhaps, this is dependent on some system policy or antivirus software. If you still have this problem and have suggestions how to improve `create_symlink`, please feel free to comment. I'll close the issue.
