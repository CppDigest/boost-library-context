# #94 - [Question] Why boost::process::pid_t is int type in Windows OS? [Open]

> Username: tt4g  
> Created at: 2019-08-06 09:15:42 UTC  
> Updated at: 2019-08-06 10:02:47 UTC  
> Url: https://github.com/boostorg/process/issues/94  

As far as I know, Windows API defines PID as `DWORD` (e.g. `GetProcessId(HANDLE)`, `GetCurrentProcessId()`).  
And it is written in the MSDN document that `DWORD` takes the value of 0 ~ 4294967295:  
  
* https://docs.microsoft.com/en-us/openspecs/windows_protocols/ms-dtyp/262627d8-3418-4627-9218-4ffe110850b2  
* https://docs.microsoft.com/en-us/windows/win32/winprog/windows-data-types#dword  
  
  
I found that `boost::process::pid_t` is an alias for` int` in Windows OS:  
  
https://github.com/boostorg/process/blob/6ccce9104ae91f6d07ac64c6d4e5fc0b57a649fe/include/boost/process/detail/windows/child_handle.hpp#L20  
  
Why is `int` instead of the same unsigned type (`unsigned int`) as the original `DWORD`?  
  
I know that `signed int` and `unsigned int` casts do not change the internal data.  
However, When passing `boost::process::child::id()` to the Windows API, it may be inconvenient because it requires casting depending on the compilation flag (/w44365).

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2019-08-06 09:34:44 UTC  
> Url: https://github.com/boostorg/process/issues/94#issuecomment-518592210  

To be honest, the actual answer is that your's truly made a mistake. I am not sure I get around to fixing that anytime soon now.

---

## Comment 2

> Username: tt4g  
> Created at: 2019-08-06 10:02:47 UTC  
> Url: https://github.com/boostorg/process/issues/94#issuecomment-518602896  

Thank you for quick reply.  
  
I noticed the C4365 warning because I turned on the warning flag /w44365 in a small program for checking usage of Boost.Process.  
  
Considering the maximum value of `DWORD`, using `boost::proces::child::id()` is not a problem in my project.
