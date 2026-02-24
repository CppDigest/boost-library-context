# #16 - Using boost::stacktrace with MinGW 5.3.0 bundled with Qt 5.8 [Closed]

> Username: abelharisov  
> Created at: 2017-04-24 06:51:07 UTC  
> Updated at: 2017-10-13 18:37:56 UTC  
> Closed at: 2017-10-13 18:37:56 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/16  

Hello! I have small example with boost::stacktrace: just setup handler, crash and write trace.  
Everything is ok, but stacktrace doesn't contains source file names and lines numbers:  
```  
Crashed:  
 0# 0x0040166D in C:\workspace\build-botsapp-Desktop_Qt_5_8_0_MinGW_32bit-Debug\booststacktracetest\debug\booststacktracetest.exe  
 1# 0x75D97790 in C:\Windows\System32\msvcrt.dll  
```  
How to fix this issue? I tried several configurations: BOOST_STACKTRACE_USE_WINDBG, BOOST_STACKTRACE_USE_BACKTRACE, BOOST_STACKTRACE_USE_LIBUNWIND. Last one even doesn't write stacktrace.  
  
Code:  
```  
#include <signal.h>  
#include <cstdlib>  
#include <iostream>  
  
#define BOOST_STACKTRACE_USE_BACKTRACE  
#include <boost/stacktrace.hpp>  
  
void my_signal_handler(int signum)  
{  
    ::signal(signum, SIG_DFL);  
    boost::stacktrace::stacktrace st;  
    std::cout << "Crashed:\n" << st << std::endl;  
    std::_Exit(-1);  
}  
  
void crash()  
{  
    throw std::runtime_error("test");  
}  
  
int main(int argc, char *argv[])  
{  
    ::signal(SIGSEGV, &my_signal_handler);  
    ::signal(SIGABRT, &my_signal_handler);  
  
    crash();  
}  
  
```

---

## Comment 1

> Username: apolukhin  
> Created at: 2017-04-27 19:41:36 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/16#issuecomment-297818382  

That's a known problem. I'll try to come up with a solution soon.

---

## Comment 2

> Username: abelharisov  
> Created at: 2017-04-29 08:26:14 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/16#issuecomment-298155482  

Thank you! I will be waiting for it.  
There is another project, [drmingw](https://github.com/jrfonseca/drmingw). It can print stack trace for mingw. I hope, it can help you in some way.

---

## Comment 3

> Username: apolukhin  
> Created at: 2017-10-13 18:37:56 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/16#issuecomment-336534476  

Dup of #14
