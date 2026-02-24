# #190 - AcceptEx (winsock) and sub-processes hangs byte sending to client [Closed]

> Username: caloni  
> Created at: 2019-01-07 21:40:59 UTC  
> Updated at: 2020-12-30 00:59:02 UTC  
> Closed at: 2020-12-30 00:59:00 UTC  
> Url: https://github.com/boostorg/asio/issues/190  

Second request in server using AcceptEx (winsock) and sub-processes hangs byte sending to client.   
  
Accordingly with Raymond Chen's post "Programmatically controlling which handles are inherited by new processes in Win32" [1] comment, socket handles (handles to DeviceAfd opened by  
 winsock using the AcceptEx API) are always inheritable by default in a CreateProcess with bInheritHandles=TRUE situation, and seems this can't be changed. This behavior is what is causing the second request hang issue after the send function is called with the subprocess output.  
  
>  
> Killer{R}  
> December 16, 2011 at 8:10 am  
> BTW socket handles (handles to DeviceAfd opened by winsock) are always inheritable by default and seems this is not changable, and this caused few very interesting (read hard to find) bugs in my experince (sic). The most complicated was heap corruption caused by overlapped socket operation that was not cancelled as app expected after called closesocket cause this app started another process and that another process inherited socket handle. Since operation was not cancelled, but app released memory (after proper closesocket) used for OVERLAPPED structure memory got randomly corrupted. Or just not corrupted and nothing visible happened if memory was decommited on the time when pending operation complete, cause it is kernel who writes into that memory.  
>  
> BTW it was svchost process, that hosts many services and even 3rd party dlls, one of which (ours) called CreateProcess wit bInheritehandles set to TRUE.  
>  
  
There are workaround solutions, as for Windows Vista or superior is to set the inheritable handles using the InitializeProcThreadAttributeList/UpdateProcThreadAttribute API, and for Windows XP must be an ad hoc implementation (e.g. create a third process, avoiding the first handle inheritance and applying in the second instance; this way there is no DeviceAfd inhereted from the parent process), but the correct fix to Windows 7 or superior is to use the flag WSA_FLAG_NO_HANDLE_INHERIT in WSASocketW function for the creation of the client socket before the AcceptEx call.  
  
In the Boost.Asio current implementation (asio/detail/impl/socket_ops.ipp, line 1395) and develop branches there is no way to set this flag programatically. This parameter is hard-coded and use only WSA_FLAG_OVERLAPPED flag [2]. Also I haven't found a way to overload the socket_opt namespace function socket in order to reimplement the call with this flag.  
  
This is a bug that can be avoided using winsock API using the non inherit handle flag, but in Boost.Asio this is an issue.  
  
[1] https://blogs.msdn.microsoft.com/oldnewthing/20111216-00/?p=8873/  
[2] https://github.com/boostorg/asio/blob/develop/include/boost/asio/detail/impl/socket_ops.ipp#L1395

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 00:58:59 UTC  
> Url: https://github.com/boostorg/asio/issues/190#issuecomment-752290996  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#665](https://github.com/chriskohlhoff/asio/issues/665).
