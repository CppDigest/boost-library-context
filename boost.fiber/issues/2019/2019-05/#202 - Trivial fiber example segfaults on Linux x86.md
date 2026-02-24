# #202 - Trivial fiber example segfaults on Linux x86 [Closed]

> Username: durandaltheta  
> Created at: 2019-05-15 17:36:29 UTC  
> Updated at: 2019-05-15 18:40:22 UTC  
> Closed at: 2019-05-15 17:50:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/202  

I've been trying to implement fibers into a project I'm working on but it always segfaults on memory location 0x0 or in fcontext from the algorithm's pick_next() call. I stepped back to try to make a trivial example but I can't get it to run without segfault either.   
  
```  
duran@duran-VirtualBox:~/dfc$ cat tst/trivial-test.cpp  
#include <iostream>  
#include <functional>  
  
#include <boost/fiber/all.hpp>  
  
int main(int argc, char** argv){  
    boost::fibers::fiber([]{  
        std::cout << "runs" << std::endl;  
    }).join();  
    return 0;  
}  
```  
  
Code compiles:  
```  
duran@duran-VirtualBox:~/dfc$ make clean; make trivial-test  
rm -rf /home/duran/dfc/build   
rm -rf /home/duran/dfc/lib  
mkdir -p /home/duran/dfc/build  
g++ -std=c++11 -g3 -D_GLIBCXX_DEBUG -I/home/duran/dfc/inc  -I/home/duran/dfc/impl /home/duran/dfc/tst/trivial-test.cpp -I/home/duran/dfc/inc  -I/home/duran/dfc/impl -L/home/duran/dfc/lib -DBOOST_LOG_DYN_LINK -lboost_log -lboost_system -lboost_fiber -lboost_coroutine -lboost_context -lpthread -o /home/duran/dfc/build/trivial-test  
```  
  
Running segfaults:  
```  
duran@duran-VirtualBox:~/dfc$ ./build/trivial-test   
Segmentation fault (core dumped)  
duran@duran-VirtualBox:~/dfc$ gdb ./build/trivial-test   
GNU gdb (Ubuntu 8.1-0ubuntu3) 8.1.0.20180409-git  
Copyright (C) 2018 Free Software Foundation, Inc.  
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>  
This is free software: you are free to change and redistribute it.  
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"  
and "show warranty" for details.  
This GDB was configured as "x86_64-linux-gnu".  
Type "show configuration" for configuration details.  
For bug reporting instructions, please see:  
<http://www.gnu.org/software/gdb/bugs/>.  
Find the GDB manual and other documentation resources online at:  
<http://www.gnu.org/software/gdb/documentation/>.  
For help, type "help".  
Type "apropos word" to search for commands related to "word"...  
Reading symbols from ./build/trivial-test...done.  
(gdb) run  
Starting program: /home/duran/dfc/build/trivial-test   
[Thread debugging using libthread_db enabled]  
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".  
  
Program received signal SIGSEGV, Segmentation fault.  
0x00007ffff797bf14 in ontop_fcontext () from /usr/lib/x86_64-linux-gnu/libboost_context.so.1.65.1  
(gdb) bt  
#0  0x00007ffff797bf14 in ontop_fcontext () from /usr/lib/x86_64-linux-gnu/libboost_context.so.1.65.1  
Backtrace stopped: Cannot access memory at address 0x0  
(gdb)   
```  
  
Same error occurs in boost 1_65 and locally compiled/installed 1_70. Current boost is 1_70:  
```  
duran@duran-VirtualBox:/usr/include/boost$ cat ./version.hpp   
//  Boost version.hpp configuration header file  ------------------------------//  
  
//  (C) Copyright John maddock 1999. Distributed under the Boost  
//  Software License, Version 1.0. (See accompanying file  
//  LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
  
//  See http://www.boost.org/libs/config for documentation  
  
#ifndef BOOST_VERSION_HPP  
#define BOOST_VERSION_HPP  
  
//  
//  Caution: this is the only Boost header that is guaranteed  
//  to change with every Boost release. Including this header  
//  will cause a recompile every time a new Boost version is  
//  used.  
//  
//  BOOST_VERSION % 100 is the patch level  
//  BOOST_VERSION / 100 % 1000 is the minor version  
//  BOOST_VERSION / 100000 is the major version  
  
#define BOOST_VERSION 107000  
  
//  
//  BOOST_LIB_VERSION must be defined to be the same as BOOST_VERSION  
//  but as a *string* in the form "x_y[_z]" where x is the major version  
//  number, y is the minor version number, and z is the patch level if not 0.  
//  This is used by <config/auto_link.hpp> to select which library version to link to.  
  
#define BOOST_LIB_VERSION "1_70"  
  
#endif  
```  
  
OS is Ubuntu Linux virtualbox virtual machine running under windows, /proc/cpuinfo:  
```  
uran@duran-VirtualBox:~/dfc/tst$ cat /proc/cpuinfo  
processor	: 0  
vendor_id	: GenuineIntel  
cpu family	: 6  
model		: 60  
model name	: Intel(R) Core(TM) i7-4710MQ CPU @ 2.50GHz  
stepping	: 3  
cpu MHz		: 2494.234  
cache size	: 6144 KB  
```

---

## Comment 1

> Username: olk  
> Created at: 2019-05-15 17:50:05 UTC  
> Url: https://github.com/boostorg/fiber/issues/202#issuecomment-492756115  

works for me on boost-1.70/branch develop

---

## Comment 2

> Username: durandaltheta  
> Created at: 2019-05-15 18:40:22 UTC  
> Url: https://github.com/boostorg/fiber/issues/202#issuecomment-492774876  

>   
>   
> works for me on boost-1.70/branch develop  
  
I just checked out boost, checked out develop in fiber and context, rebuilt and installed at /usr (now on 1_71), manually deleted boost in /usr/local so I should only have one copy.  
  
Still same issue. Are there any flag configurations for my hardware that I should enable? I've been at this issue for over a week drilling through the boost fiber documentation
