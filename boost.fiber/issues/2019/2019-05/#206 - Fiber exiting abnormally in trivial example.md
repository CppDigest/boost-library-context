# #206 - Fiber exiting abnormally in trivial example [Closed]

> Username: durandaltheta  
> Created at: 2019-05-23 18:55:33 UTC  
> Updated at: 2019-06-27 09:26:50 UTC  
> Closed at: 2019-06-27 09:26:50 UTC  
> Url: https://github.com/boostorg/fiber/issues/206  

This may be related to closed/not fixed ticket [Trivial fiber example segfaults on Linux x86](https://github.com/boostorg/fiber/issues/202).  
  
Attempted again in Windows this time instead of Linux VM. Checked out development boost branch (1.70+). Continued to get consistent ontop_fcontext errors.  
  
Rebuilt boost fiber with [WinFiber](https://www.boost.org/doc/libs/1_70_0/libs/fiber/doc/html/fiber/overview/implementations__fcontext_t__ucontext_t_and_winfiber.html) using this command:  
`.\b2 --build-dir=C:\Users\me\myproject\boost toolset=gcc context-impl=winfib`  
  
Code build command:  
```  
C:\Users\dennbla\dfc>make trivial-test  
Makefile:38: warning: overriding recipe for target '/cygdrive/c/Users/dennbla/dfc/lib'  
Makefile:35: warning: ignoring old recipe for target '/cygdrive/c/Users/dennbla/dfc/lib'  
mkdir -p /cygdrive/c/Users/dennbla/dfc/build  
g++ -std=c++11 -g3 -D_GLIBCXX_DEBUG  -I/cygdrive/c/Users/dennbla/dfc/inc  -I/cygdrive/c/Users/dennbla/dfc/impl -I/cygdri  
ve/c/Users/dennbla/dfc/boost/include/boost-1_70  /cygdrive/c/Users/dennbla/dfc/tst/trivial-test.cpp -I/cygdrive/c/Users/  
dennbla/dfc/inc  -I/cygdrive/c/Users/dennbla/dfc/impl -I/cygdrive/c/Users/dennbla/dfc/boost/include/boost-1_70  -L/cygdr  
ive/c/Users/dennbla/dfc/lib  -L/cygdrive/c/Users/dennbla/dfc/boost/lib -DBOOST_USE_WINFIB -DBOOST_LOG_DYN_LINK -lboost_l  
og-gcc7-mt-x64-1_70 -lboost_system-gcc7-mt-x64-1_70 -lboost_fiber-gcc7-mt-x64-1_70 -lboost_coroutine-gcc7-mt-x64-1_70 -l  
boost_context-gcc7-mt-x64-1_70 -lgcc -o /cygdrive/c/Users/dennbla/dfc/build/trivial-test.exe  
```  
  
This code fails to execute, all I'm trying to do is get it to run:  
```  
#include <fstream>  
#include <iostream>  
#include <mutex>  
  
#include "boost/fiber/all.hpp"  
  
int g_num;  
  
void test() {  
    g_num = 6;  
    std::cout << "we ran!: " << g_num << std::endl;   
    std::ofstream fi;  
    fi.open("tst.txt");  
    fi << "!!!!!!!!!!!!!!!!!!!!!!!!!we ran!\n";  
    fi.close();  
    return;  
}  
  
int main(int argc, char** argv){  
    g_num = 0;  
      
    std::cout << "!!!!!!!!!!!!!!!!!!!!!about to run!: " << g_num << std::endl;   
  
    boost::fibers::fiber(test).join();  
  
    std::cout << "!!!!!!!!!!!!!!!!!!!!!we ran!: " << g_num << std::endl;   
  
    return 0;  
}  
```  
  
Execution:  
```  
C:\Users\dennbla\dfc>.\build\trivial-test.exe  
!!!!!!!!!!!!!!!!!!!!!about to run!: 0  
```  
  
Gdb execution:  
```  
C:\Users\dennbla\dfc>gdb .\build\trivial-test.exe  
GNU gdb (GDB) (Cygwin 8.1.1-1) 8.1.1  
Copyright (C) 2018 Free Software Foundation, Inc.  
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
This is free software: you are free to change and redistribute it.  
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"  
and "show warranty" for details.  
This GDB was configured as "x86_64-pc-cygwin".  
Type "show configuration" for configuration details.  
For bug reporting instructions, please see:  
<http://www.gnu.org/software/gdb/bugs/>.  
Find the GDB manual and other documentation resources online at:  
<http://www.gnu.org/software/gdb/documentation/>.  
For help, type "help".  
Type "apropos word" to search for commands related to "word"...  
Reading symbols from .\build\trivial-test.exe...done.  
(gdb) run  
Starting program: /cygdrive/c/Users/dennbla/dfc/build/trivial-test.exe  
[New Thread 12180.0x2c3c]  
[New Thread 12180.0x3640]  
[New Thread 12180.0x2c6c]  
[New Thread 12180.0xb28]  
[New Thread 12180.0x33a0]  
!!!!!!!!!!!!!!!!!!!!!about to run!: 0  
[Thread 12180.0x2c6c exited with code 3221225477]  
[Thread 12180.0x3640 exited with code 3221225477]  
[Thread 12180.0xb28 exited with code 3221225477]  
[Inferior 1 (process 12180) exited with code 030000000005]  
(gdb) bt  
No stack.  
```  
Gdb stepping  
```  
(gdb) step  
boost::context::detail::fiber_activation_record::resume (this=0x60008d100)  
    at /cygdrive/c/Users/dennbla/dfc/boost/include/boost-1_70/boost/context/fiber_winfib.hpp:117  
117             from = current();  
(gdb) step  
120             current() = this;  
(gdb) step  
123             ::SwitchToFiber( fiber);  
(gdb) step  
[Thread 15964.0x2e48 exited with code 3221225477]  
[Thread 15964.0x2b48 exited with code 3221225477]  
[Thread 15964.0x73c exited with code 3221225477]  
[Thread 15964.0xb98 exited with code 3221225477]  
[Thread 15964.0x3b30 exited with code 3221225477]  
[Thread 15964.0x390c exited with code 3221225477]  
[Inferior 1 (process 15964) exited with code 030000000005]  
(gdb) list  
118             // store `this` in static, thread local pointer  
119             // `this` will become the active (running) context  
120             current() = this;  
121             // context switch from parent context to `this`-context  
122             // context switch  
123             ::SwitchToFiber( fiber);  
124     #if defined(BOOST_NO_CXX14_STD_EXCHANGE)  
125             return detail::exchange( current()->from, nullptr);  
126     #else  
127             return std::exchange( current()->from, nullptr);  
(gdb) bt  
No stack.  
(gdb) print fiber  
```

---

## Comment 1

> Username: olk  
> Created at: 2019-05-24 05:27:13 UTC  
> Url: https://github.com/boostorg/fiber/issues/206#issuecomment-495477112  

try a larger stack
