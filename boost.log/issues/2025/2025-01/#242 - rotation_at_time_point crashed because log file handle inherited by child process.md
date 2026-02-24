# #242 - rotation_at_time_point crashed because log file handle inherited by child process [Closed]

> Username: UMU618  
> Created at: 2025-01-15 04:51:57 UTC  
> Updated at: 2025-01-19 19:07:53 UTC  
> Closed at: 2025-01-15 12:42:03 UTC  
> Url: https://github.com/boostorg/log/issues/242  

I have a main process used Boost.Log and Boost.Process V2 to create 2 child-prcesses.  
  
When both max_file and rotation_at_time_point satisfied, the main process crashed. `System Informer` shows the 2 child-prcesses inherited the log file handle.  
  
If don't create child-prcesses, won't crash.  
  
So, how to make text_file_backend don't inherit handles?

---

## Comment 1

> Username: UMU618  
> Created at: 2025-01-15 07:57:17 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2591870237  

More info: The `boost::process::v2::popen` always sets `launcher.inherit_handles = true;`.

---

## Comment 2

> Username: UMU618  
> Created at: 2025-01-15 08:54:52 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2591985632  

since C++26, we have `std::basic_ofstream<CharT,Traits>::native_handle`, can be solved by set_open_handler() and `SetHandleInformation(native_handle, HANDLE_FLAG_INHERIT, 0);`  
  
But we don't have C++26...

---

## Comment 3

> Username: UMU618  
> Created at: 2025-01-15 12:42:03 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2592739424  

Solved by ugly codes hack from MSVC filebuf.

---

## Comment 4

> Username: Lastique  
> Created at: 2025-01-15 15:58:54 UTC  
> Updated at: 2025-01-15 16:02:02 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2593315975  

> When both max_file and rotation_at_time_point satisfied, the main process crashed.  
  
Is it actually a crash (e.g. a segmentation fault) and not an uncaught exception? I suspect it's the latter. In this case the library provides a way to set exception handlers at various levels. Though perhaps the better solution would be to prevent inheriting file handles by the child processes and configure logging in the processes from scratch.  
  
Note that multiple processes *must not* use the same file for writing their logs as this will result in corrupted file contents. You should also avoid managing the same target directory by multiple processes concurrently as this will be prone to filesystem races.  
  
> So, how to make text_file_backend don't inherit handles?  
  
File backends don't control low-level file handles and don't take any measures regarding process forking. This is one of the reasons why Boost.Log [does not support process forking](https://www.boost.org/doc/libs/1_87_0/libs/log/doc/html/log/rationale/fork_support.html). I take it you're on Windows, but if you manage to inherit file handles in the child processes the point re. forking is still valid.  
  
> More info: The `boost::process::v2::popen` always sets `launcher.inherit_handles = true;`.  
  
I have no knowledge of Boost.Process, but from a cursory look [`inherit_handles`](https://github.com/boostorg/process/blob/a2d2753aa8fe4f58b163f76d17d1c43f144146bc/include/boost/process/v2/windows/default_launcher.hpp#L212-L213) is a list of handles, which is empty by default. Presumably, it is being populated in [`process_stdio` constructor](https://github.com/boostorg/process/blob/a2d2753aa8fe4f58b163f76d17d1c43f144146bc/include/boost/process/v2/stdio.hpp#L337-L340), which only adds console handles to the list. As I understand it, Boost.Process is supposed to only inherit these three handles and no others. If this is not what happens, I would consider it a problem in Boost.Process. Please, report it [there](https://github.com/boostorg/process/issues).

---

## Comment 5

> Username: UMU618  
> Created at: 2025-01-17 20:19:39 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2599126719  

@Lastique   
  
> Is it actually a crash  
  
![Image](https://github.com/user-attachments/assets/3ff13dbf-020f-4752-bd77-8d6087b9f8a3)  
  
![Image](https://github.com/user-attachments/assets/b06f86ba-c77f-4ef7-b24c-b065a63a480c)  
  
`core::set_exception_handler` can't caught it.  
  
I don't consider it an issue of Boost.Process, when it sets `launcher.inherit_handles = true`, not just the three handles, but all inheritable handles are also inherit. If not set `launcher.inherit_handles = true`, the three handles will not able to inherit.

---

## Comment 6

> Username: Lastique  
> Created at: 2025-01-17 21:31:12 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2599249817  

From the stacktrace, it looks like `backend` is an invalid (dangling) reference at the point of the `consume` call. The reference is created by dereferencing `m_pBackend` probably [here](https://github.com/boostorg/log/blob/6366d73335d298c8c5c7f7bd80f6931fedb0df1b/include/boost/log/sinks/async_frontend.hpp#L386). `m_pBackend` is a [`const boost::shared_ptr`](https://github.com/boostorg/log/blob/6366d73335d298c8c5c7f7bd80f6931fedb0df1b/include/boost/log/sinks/async_frontend.hpp#L241), meaning that it cannot be changed after the frontend construction. The only way I can imagine how the reference can get invalid is if you're destroying the sink frontend without first joining the log record feeding thread. The frontend destructor implicitly calls `stop` and this call does not ensure that the feeding thread has returned from `run`, as is noted [here](https://github.com/boostorg/log/blob/6366d73335d298c8c5c7f7bd80f6931fedb0df1b/include/boost/log/sinks/async_frontend.hpp#L410-L414).  
  
Other than this, I do not see how `m_pBackend` could become invalid, barring some weird compiler bug or memory issue.  
  
> I don't consider it an issue of Boost.Process, when it sets `launcher.inherit_handles = true`, not just the three handles, but all inheritable handles are also inherit. If not set `launcher.inherit_handles = true`, the three handles will not able to inherit.  
  
Again, I'm assuming by `launcher.inherit_handles` you mean [`default_launcher::inherit_handles`](https://github.com/boostorg/process/blob/a2d2753aa8fe4f58b163f76d17d1c43f144146bc/include/boost/process/v2/windows/default_launcher.hpp#L212-L213), and that is clearly not a `bool`. If you mean the `bInheritHandles` argument to [`CreateProcess`](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-createprocessa) then sure, Boost.Process must pass `TRUE` in it, that's not the problem. The problem is that in the created process Boost.Process does not close all open handles that are not in the `default_launcher::inherit_handles` list, which is what I would expect it should do. I'm not sure what is Boost.Process' way to implement this, and whether the user is supposed to do something to enable this, but either way if Boost.Process doesn't do this I'd consider it an implementation bug, and if it's simply impossible to do this - it's a design bug (because what's the point in having a list in the first place).  
  
In any case, as I said, Boost.Log does not support this use case.

---

## Comment 7

> Username: UMU618  
> Created at: 2025-01-18 10:44:22 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2599665495  

> Again, I'm assuming by you mean [`default_launcher::inherit_handles`](https://github.com/boostorg/process/blob/a2d2753aa8fe4f58b163f76d17d1c43f144146bc/include/boost/process/v2/windows/default_launcher.hpp#L212-L213)  
  
I mean this line: https://github.com/boostorg/process/blob/9561ebad1c1170ed7a489fc1ced8425f34ce84bc/include/boost/process/v2/stdio.hpp#L311

---

## Comment 8

> Username: Lastique  
> Created at: 2025-01-18 11:12:58 UTC  
> Updated at: 2025-01-18 11:13:52 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2599675061  

> > Again, I'm assuming by you mean [`default_launcher::inherit_handles`](https://github.com/boostorg/process/blob/a2d2753aa8fe4f58b163f76d17d1c43f144146bc/include/boost/process/v2/windows/default_launcher.hpp#L212-L213)  
>   
> I mean this line: https://github.com/boostorg/process/blob/9561ebad1c1170ed7a489fc1ced8425f34ce84bc/include/boost/process/v2/stdio.hpp#L311  
  
This was changed in [2275057574cb4bcf8d1db4781198a1c336a63638](https://github.com/boostorg/process/commit/2275057574cb4bcf8d1db4781198a1c336a63638), though it's not in master yet. The updated code seems to be using [`UpdateProcThreadAttribute`/`PROC_THREAD_ATTRIBUTE_HANDLE_LIST`](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-updateprocthreadattribute) to limit inheriting file handles.

---

## Comment 9

> Username: UMU618  
> Created at: 2025-01-18 14:08:14 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2599731042  

> The updated code seems to be using [`UpdateProcThreadAttribute`/`PROC_THREAD_ATTRIBUTE_HANDLE_LIST`](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-updateprocthreadattribute) to limit inheriting file handles.  
  
`UpdateProcThreadAttribute` only adds some extra handles if provided, not to delete those inheritable handles. Need to get the handle value to remove it, but now we don't have C++ 26 to get OS handle from a `std::basic_ofstream`.

---

## Comment 10

> Username: Lastique  
> Created at: 2025-01-18 17:48:01 UTC  
> Updated at: 2025-01-18 17:50:51 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2599806462  

> > The updated code seems to be using [`UpdateProcThreadAttribute`/`PROC_THREAD_ATTRIBUTE_HANDLE_LIST`](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-updateprocthreadattribute) to limit inheriting file handles.  
>   
> `UpdateProcThreadAttribute` only adds some extra handles if provided, not to delete those inheritable handles.  
  
This is not how I understand [`CreateProcess`](https://learn.microsoft.com/en-us/windows/win32/api/processthreadsapi/nf-processthreadsapi-createprocessa) description:  
  
> By default, passing **TRUE** as the value of the *bInheritHandles* parameter causes all inheritable handles to be inherited by the new process. This can be problematic for applications which create processes from multiple threads simultaneously yet desire each process to inherit different handles.  
>  
> Applications can use the [UpdateProcThreadAttributeList](https://learn.microsoft.com/en-us/windows/desktop/api/processthreadsapi/nf-processthreadsapi-updateprocthreadattribute) function with the **PROC_THREAD_ATTRIBUTE_HANDLE_LIST** parameter to provide a list of handles to be inherited by a particular process.  
  
If you specify `bInheritHandles = TRUE` and don't have `PROC_THREAD_ATTRIBUTE_HANDLE_LIST` set for the calling thread then all inheritable handles are inherited by the child process. If `PROC_THREAD_ATTRIBUTE_HANDLE_LIST` is set then only those handles in the list are inherited.

---

## Comment 11

> Username: UMU618  
> Created at: 2025-01-19 14:00:38 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2600873981  

> If `PROC_THREAD_ATTRIBUTE_HANDLE_LIST` is set then only those handles in the list are inherited.  
  
I sure hope you are right. This is a good solution, and I'll try this when Boost 1.88 is released and available on vcpkg.

---

## Comment 12

> Username: Lastique  
> Created at: 2025-01-19 16:29:59 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2600932552  

I suppose, you could try it now yourself by calling [`UpdateProcThreadAttributeList`](https://learn.microsoft.com/en-us/windows/desktop/api/processthreadsapi/nf-processthreadsapi-updateprocthreadattribute) before `popen` in your older Boost version, if you don't mind calling WinAPI directly. Just remember to remove the call when you switch to the updated Boost.

---

## Comment 13

> Username: UMU618  
> Created at: 2025-01-19 19:07:51 UTC  
> Url: https://github.com/boostorg/log/issues/242#issuecomment-2600984418  

@Lastique Yes, you are right. [UpdateProcThreadAttributeList](https://learn.microsoft.com/en-us/windows/desktop/api/processthreadsapi/nf-processthreadsapi-updateprocthreadattribute) can exclude the inheritable handles other than those specified explicitly. Let's hope Boost 1.88 is released soon, then we'll have a more compatible solution.
