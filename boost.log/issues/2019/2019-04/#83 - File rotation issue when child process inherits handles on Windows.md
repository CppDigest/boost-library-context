# #83 - File rotation issue when child process inherits handles on Windows. [Closed]

> Username: NVCherney  
> Created at: 2019-04-24 19:20:12 UTC  
> Updated at: 2019-04-26 10:11:59 UTC  
> Closed at: 2019-04-25 12:10:26 UTC  
> Url: https://github.com/boostorg/log/issues/83  

On Windows7+mingw64+boost1.70 I've created couple of .exe (say parent.exe and child.exe), both using boost.log (both with same keywords::rotation_size set in text_file_backend ctor, but different keywords::file_name and keywords::target in make_collector()). The parent.exe starts child.exe (using boost.process) and communicates through stdin/stdout pipe. Then I've stumbled upon unwanted spontaneous exception being thrown from text_file_backend.cpp...move_file()...filesystem::rename(from, to); at rotate_file()...store_file() function call in parent.exe, with no evident reason to this; the exception says that file is used by another process (the problematic file is that to be moved to keywords::target dir after rotation_size exceeded).  
The ProcessExplorer shows that the only process to use the log file being moved is the child.exe, apparently 'cause boost.process starts the child.exe with file handles inherited (implying the log file currently being written).  
Is it possible to mitigate this exception "out of nowhere", possibly opening text file non-inheritable or provide text file native handle access to mark it non-inheritable?

---

## Comment 1

> Username: Lastique  
> Created at: 2019-04-24 19:34:12 UTC  
> Url: https://github.com/boostorg/log/issues/83#issuecomment-486394652  

> Is it possible to mitigate this exception "out of nowhere", possibly opening text file non-inheritable or provide text file native handle access to mark it non-inheritable?  
  
No, Boost.Log uses standard streams to work with files, there's no access to low level handles. Even if there was, I'm reluctant to provide a non-portable API on Boost.Log level.  
  
I'm not sure I understand what causes the problem. If there's only one process working with a given file, there shouldn't be a problem. Unless there's some Windows-specific quirk I'm missing. Could you provide more details? What kind of error happens, what is the error code and message?

---

## Comment 2

> Username: NVCherney  
> Created at: 2019-04-25 04:01:13 UTC  
> Updated at: 2019-04-25 04:39:52 UTC  
> Url: https://github.com/boostorg/log/issues/83#issuecomment-486511686  

> > Is it possible to mitigate this exception "out of nowhere", possibly opening text file non-inheritable or provide text file native handle access to mark it non-inheritable?  
>   
> No, Boost.Log uses standard streams to work with files, there's no access to low level handles. Even if there was, I'm reluctant to provide a non-portable API on Boost.Log level.  
>   
> I'm not sure I understand what causes the problem. If there's only one process working with a given file, there shouldn't be a problem. Unless there's some Windows-specific quirk I'm missing. Could you provide more details? What kind of error happens, what is the error code and message?  
  
Stack (in attachment) in debugger shows error_num=32 (corresponds to ERROR_SHARING_VIOLATION) in stack frame  
`bool error(err_t error_num, const path& p1, const path& p2, error_code* ec,  const char* message)  
`  
The macro BOOST_ERRNO expands to ::GetLastError() afaik in previous stack frame  
```  
  void rename(const path& old_p, const path& new_p, error_code* ec)  
  {  
    error(!BOOST_MOVE_FILE(old_p.c_str(), new_p.c_str()) ? BOOST_ERRNO : 0, old_p, new_p,  
      ec, "boost::filesystem::rename");  
  }  
```  
  
The exception caught shows (i'm using russian Win7 64bit; parent and child are 32bit though)  
boost::filesystem::rename: Процесс не может получить доступ к файлу, так как этот файл занят другим процессом: "C:\Work\ToriControl\TestDir\TestControlLog_00015.log", "C:\Work\ToriControl\TestDir\TestControlLogDir\TestControlLog_00015.log"  
  
P.S. Manually closing inherited file handle in child process (using ProcessExplorer) magically cures this problem.  
P.P.S. Both parent and child processes are multithreaded and are logging intensively, if it matters.  
P.P.P.S. Actually there's a lot of child processes (usually ten; still all processes have unique keywords::file_name and keywords::target) per single parent process.  
  
[2019.04.25 stack.txt](https://github.com/boostorg/log/files/3115023/2019.04.25.stack.txt)

---

## Comment 3

> Username: Lastique  
> Created at: 2019-04-25 12:10:26 UTC  
> Url: https://github.com/boostorg/log/issues/83#issuecomment-486646092  

Ok, let me summarise. You spawn an number of child processes from a parent process. All child processes inherit file handles from the parent. When the *parent* process tries to rotate the file that is also held open by (one or more) child process, you get an exception. Is this correct?  
  
If so, I'm not sure what I can do in Boost.Log. The child processes cannot access the file handle, it is effectively leaked. In the parent process I can't access the file handle, unless I reimplement iostreams, which I'm really not looking forward to. I also have no atfork handler on Windows.  
  
I think, the best approach would be to request Boost.Process to not inherit file handles in the child processes. In Windows, `CreateProcess` has `bInheritHandles` parameter for that; in POSIX, Boost.Process would have to iterate over `/proc/self/fd` file descriptors after `fork` and close them (except for fd 0, 1 and 2, which are stdout, stderr and stdin). If there isn't such functionality implemented already, it would be useful beyond logging as well.

---

## Comment 4

> Username: NVCherney  
> Created at: 2019-04-25 16:57:03 UTC  
> Updated at: 2019-04-25 16:58:29 UTC  
> Url: https://github.com/boostorg/log/issues/83#issuecomment-486754142  

> Ok, let me summarise. You spawn an number of child processes from a parent process. All child processes inherit file handles from the parent. When the parent process tries to rotate the file that is also held open by (one or more) child process, you get an exception. Is this correct?  
  
Mostly correct, as far as I understand Windows behaviour.  
  
>In the parent process I can't access the file handle, unless I reimplement iostreams, which I'm really not looking forward to.  
  
It's not very harsh task with boost.iostream but I'm not sure if the benefits worth the pain.  
  
> I think, the best approach would be to request Boost.Process to not inherit file handles in the child processes. In Windows, CreateProcess has bInheritHandles parameter for that;  
  
I still have troubles with that 'cause setting bInheritHandles=FALSE in the parent (that's feasible in boost.process on_setup with windows_executor) impedes reading stdin in the child (an attempt to read stdin in child produces bad file descriptor error).  
Now I'm investigating heavily how to prevent all file handles to be uncontrollably inherited by child (I've tried to restrict inherited handle array to stdin, stdout pipe handles using UpdateProcThreadAttribute() with PROC_THREAD_ATTRIBUTE_HANDLE_LIST but did not succeed; that leaves me with same "bad file descriptor" error in the child).  
If there isn't going to be any luck for me with inheritance restriction - I'll be left with palliative solution like enumerating all file handles in child process using NtQuerySystemInformation() and closing log file explicitly.

---

## Comment 5

> Username: Lastique  
> Created at: 2019-04-25 17:13:27 UTC  
> Url: https://github.com/boostorg/log/issues/83#issuecomment-486759858  

> I still have troubles with that 'cause setting bInheritHandles=FALSE in the parent (that's feasible in boost.process on_setup with windows_executor) impedes reading stdin in the child (an attempt to read stdin in child produces bad file descriptor error).  
  
I think, you should ask Boost.Process maintainer for support about this. Having a working console in the child process should be possible.

---

## Comment 6

> Username: Lastique  
> Created at: 2019-04-25 17:29:21 UTC  
> Url: https://github.com/boostorg/log/issues/83#issuecomment-486765778  

Also, see [this PR](https://github.com/klemens-morgenstern/boost-process/pull/196) that limits file handle inheritance in child processes (both on POSIX and Windows). I think, @klemens-morgenstern will appreciate your feedback.

---

## Comment 7

> Username: NVCherney  
> Created at: 2019-04-25 17:34:38 UTC  
> Url: https://github.com/boostorg/log/issues/83#issuecomment-486767585  

Thanks for help and good luck with development.

---

## Comment 8

> Username: NVCherney  
> Created at: 2019-04-26 10:11:59 UTC  
> Url: https://github.com/boostorg/log/issues/83#issuecomment-487005843  

...and by the way UpdateProcThreadAttribute() with PROC_THREAD_ATTRIBUTE_HANDLE_LIST works well, but its input handle list must outlive CreateProcess() call (that I didn't get, silly me).
