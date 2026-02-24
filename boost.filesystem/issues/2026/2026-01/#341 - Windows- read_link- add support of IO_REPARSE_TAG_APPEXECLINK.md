# #341 - Windows: read_link: add support of IO_REPARSE_TAG_APPEXECLINK [Closed]

> Username: benoitdudu  
> Created at: 2026-01-06 12:56:18 UTC  
> Updated at: 2026-01-08 07:18:14 UTC  
> Closed at: 2026-01-06 22:21:40 UTC  
> Url: https://github.com/boostorg/filesystem/issues/341  

Hello,  
  
Would it be possible to add the support of IO_REPARSE_TAG_APPEXECLINK in the read_link function https://github.com/boostorg/filesystem/blob/develop/src/operations.cpp#L4559 ?  
  
I found this stackoverflow that explains a bit the way to find the link of a 'reparse_tag_execlink' https://stackoverflow.com/questions/62474046/how-do-i-find-the-target-of-a-windows-app-execution-alias-in-c-win32-api  
  
The stackoverflow answer points to this website that explains the way to retrieve the link of this reparse tag: https://www.tiraniddo.dev/2019/09/overview-of-windows-execution-aliases.html

---

## Comment 1

> Username: Lastique  
> Created at: 2026-01-06 22:21:38 UTC  
> Url: https://github.com/boostorg/filesystem/issues/341#issuecomment-3716577560  

From the comments in that StackOverflow question, Microsoft has [removed](https://github.com/PowerShell/PowerShell/pull/16044) support for AppExeCLinks from PowerShell, noting that they don't want users to interpret this kind of reparse points. Also, it seems like the target path does not always correspond to the executable that is actually launched though this reparse point, and the target path alone is not enough to launch it anyway, so I'm not sure how useful it would be to resolve this reparse point as a symlink. Given this, I'm leaning towards not supporting this new kind of reparse points.  
  
Thanks for the report and the additional info, though. It was insightful.

---

## Comment 2

> Username: benoitdudu  
> Created at: 2026-01-07 15:16:54 UTC  
> Url: https://github.com/boostorg/filesystem/issues/341#issuecomment-3719352334  

Hi,  
  
Initially my intent was to use boost.process to start processes.  
One of these process is 'python' for which I unfortunately used Windows Store to install.  
  
In my terminal, I simply call 'python' and it starts. If I run 'where python' it points to the reparse appexeclink... So I was expecting boost.process to find it and to launch it... It was a bit optimistic :)  
  
Some other application (for instance cygwin) resolve this reparse point as symlink.

---

## Comment 3

> Username: benoitdudu  
> Created at: 2026-01-08 07:18:14 UTC  
> Url: https://github.com/boostorg/filesystem/issues/341#issuecomment-3722432795  

The cygwin code: https://github.com/msys2/msys2-runtime/blob/msys2-3.6.5/winsup/cygwin/path.cc#L2908
