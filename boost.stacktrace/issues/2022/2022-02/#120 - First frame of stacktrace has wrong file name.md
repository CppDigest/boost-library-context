# #120 - First frame of stacktrace has wrong file name [Closed]

> Username: raccog  
> Created at: 2022-02-05 00:49:58 UTC  
> Updated at: 2022-04-01 18:48:10 UTC  
> Closed at: 2022-04-01 18:48:10 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/120  

### Description  
  
When running the simple program below, saved as ```test.cpp```;  
```c++  
#include <iostream>  
#define BOOST_STACKTRACE_USE_ADDR2LINE  
#include <boost/stacktrace.hpp>  
  
int main() {  
    std::cout << boost::stacktrace::stacktrace();  
}  
```  
the output is:  
```  
 0# main at /usr/include/boost/stacktrace/stacktrace.hpp:129  
 1# __libc_start_main in /usr/lib/libc.so.6  
 2# _start in ./a.out  
```  
The first frame lists the wrong source file and line position. This output should be:  
```  
 0# main at /<SOURCE_DIRECTORY>/test.cpp:6  
 1# __libc_start_main in /usr/lib/libc.so.6  
 2# _start in ./a.out  
```  
where ```<SOURCE_DIRECTORY>``` is the directory containing the ```test.cpp``` source file.  
  
### Steps to Reproduce  
  
1. Install boost using Linux package manager.  
2. Compile the program using the command:  
```  
g++ -g -no-pie -fno-pie -rdynamic -ldl -lboost_stacktrace_addr2line test.cpp  
```  
3. Run the program  
  
### System  
OS: Linux 5.16.3  
Boost Version: 1.78.0  
Compiler: gcc 11.1.0  
  
### Questions  
  
1. Has anyone else ran into this issue?  
2. Could I be missing a compiler flag or preprocessor definition that would fix this?

---

## Comment 1

> Username: raccog  
> Created at: 2022-04-01 18:48:10 UTC  
> Url: https://github.com/boostorg/stacktrace/issues/120#issuecomment-1086223045  

After some further digging, I have found out that this problem originates from GNU addr2line. So I am marking this issue as closed.
