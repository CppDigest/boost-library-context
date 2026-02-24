# #56 - can't write dump file on windows [Closed]

> Username: yokarno  
> Created at: 2018-06-14 12:22:32 UTC  
> Updated at: 2026-01-06 14:43:20 UTC  
> Closed at: 2018-06-15 10:26:42 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/56  

Hello:  
  I write a test program and want to write dump file on windows, but it doesn't work, I think it has something wrong in my code, but I don't know why , can someone help me  :  
my whole code, on Windows 10 ,VS2010  boost 1.67:  
  
#include <signal.h>  
#include <boost/stacktrace.hpp>  
  
void my_signal_handler(int signum)  
{  
    ::signal(signum, SIG_DFL);  
    boost::stacktrace::safe_dump_to( "./backtrace.dump");  
    ::raise(SIGABRT);  
}  
  
int main()  
{  
    ::signal(SIGSEGV, &my_signal_handler);  
    ::signal(SIGABRT, &my_signal_handler);  
  
 //do something;  
    std::terminate();  
}  
  
is it some macro i missed ?   
  
thanks.

---

## Comment 1

> Username: apolukhin  
> Created at: 2018-06-15 07:44:51 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/56#issuecomment-397541290  

I'm afraid that Windows is a platform that does not have functionality for getting async signal safe stacktarce.  
  
Looks like I have to specify the supported platforms here: https://www.boost.org/doc/libs/1_67_0/doc/html/stacktrace/getting_started.html#stacktrace.getting_started.handle_terminates_aborts_and_seg

---

## Comment 2

> Username: yokarno  
> Created at: 2018-06-15 10:26:42 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/56#issuecomment-397580224  

unfortunately news, but thank you anyway.

---

## Comment 3

> Username: aholzinger  
> Created at: 2026-01-04 17:03:41 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/56#issuecomment-3708253200  

Hi Antony,  
  
could you please share a link with information about the problem on Windows (the source code names MSDN, but there's no link)?  
  
I read the MSDN documentation of Win32 functions CreateFile and WriteFile and I can't see where the problem is.  
https://learn.microsoft.com/en-us/cpp/c-runtime-library/reference/signal doesn't list CreateFile or WriteFile as forbidden (but malloc).  
  
Thank you.

---

## Comment 4

> Username: apolukhin  
> Created at: 2026-01-04 19:05:41 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/56#issuecomment-3708341437  

@aholzinger the documentation contains  
```  
Don't use any function that generates a system call  
```  
`CreateFile` and `WriteFile` are system calls (at least their alternatives in POSIX are)

---

## Comment 5

> Username: aholzinger  
> Created at: 2026-01-05 11:11:13 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/56#issuecomment-3709989233  

This documentation talks only about CRT functions and **not** about Win32 functions. The term "system call" is not a Windows OS term, so I doubt that `CreateFile` or `WriteFile` are meant here. The same applies for Win32 memory management functions like `VirtualAlloc`. I can't understand why the Windows OS should rely internally on the signal handling of the CRT which has nothing to do with SEH. IMO (I'm not a Windows CRT expert) when a crash happens on Windows a structured exeption will be thrown and the handler will then invoke the CRT signal handler. So why shouldn't one use Win32 functions in the signal handler?  
  
The #ifdefed away `dump` functions are no signal handlers, so IMO it should be left to the user to use them. An `#if 0` IMO is never a good idea. At least some real #define like `#if BOOST_STACKTRACE_WINDOWS_PERMIT_DUMP` should be used so that the user can decide if he/she wants to use the function.

---

## Comment 6

> Username: aholzinger  
> Created at: 2026-01-05 17:30:01 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/56#issuecomment-3711397357  

Example 3 uses `printf` in a structured exception handler: https://learn.microsoft.com/en-us/windows/win32/debug/using-an-exception-handler?source=recommendations#example-3  
  
The documentation of the CRT signal handler function says don't do this. So I would expect that it's perfectly fine to call Win32 functions in the CRT signal handler (which is the other way round).

---

## Comment 7

> Username: apolukhin  
> Created at: 2026-01-06 10:18:44 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/56#issuecomment-3714100970  

@aholzinger safe_dump examples sometimes deadlock on Windows. If you wish to fix that, report the issues to Windows developers and after the fixes, open a new feature request to this library with links to resolved issues

---

## Comment 8

> Username: aholzinger  
> Created at: 2026-01-06 11:21:51 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/56#issuecomment-3714335212  

@apolukhin I looked in the example, src and test subdirectories, but didn't find any safe_sump examples. Would you be so kind a give a hint please?

---

## Comment 9

> Username: apolukhin  
> Created at: 2026-01-06 14:43:20 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/56#issuecomment-3714969390  

@aholzinger the tests could be found here https://github.com/boostorg/stacktrace/blob/boost-1.84.0/example/terminate_handler.cpp  
  
`run_3` and `run_4` tests are removed in develop. They rarely fail on Windows, even though they have no syscalls. Other tests in this file may also fail on Windows, for unknown reasons
