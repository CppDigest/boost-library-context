# #45 - Same executable behaves differently (exception handling) being run with Wine and in native Windows [Closed]

> Username: ntff  
> Created at: 2022-06-09 21:59:10 UTC  
> Updated at: 2022-07-05 05:54:36 UTC  
> Closed at: 2022-07-05 05:54:36 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/45  

Can you, please, give my any ideas on how to resolve the following problem? Thank you, in advance.  
  
I would like to build my program with MinGW compiler and run with Wine, and do it all on Linux.  
  
I reduced my code to the piece, I think, represents the **PROBLEM**: the resulting executable doesn't catch exceptions, thrown from coroutine and, instead, immediately terminates when running by Wine emulator. Exactly the same executable, being run in native Windows, behaves correctly and catches thrown exceptions as expected. As I would like to run executable with Wine, I need it behave the same way as in native Windows.  
  
My example code is `test.cc`:  
```cpp  
#include <boost/coroutine2/all.hpp>  
#include <stdexcept>  
#include <iostream>  
  
using boost::coroutines2::coroutine;  
  
void cooperative(coroutine<void>::push_type &sink)  
{  
  sink();  
  try  
  {  
    throw std::runtime_error("error");  
  }  
  catch (const std::runtime_error &e)  
  {  
    std::cerr << __LINE__ << ':' << e.what() << '\n';  
  }  
}  
  
int main()  
{  
  coroutine<void>::pull_type source{cooperative};  
  try  
  {  
    source();  
  }  
  catch (const std::runtime_error &e)  
  {  
    std::cerr << __LINE__ << ':' << e.what() << '\n';  
  }  
}  
```  
  
I build it into executable `test.exe` with the script:  
```shell  
#!/bin/sh  
  
BOOST_HOME=~/.conan/data/boost/1.79.0/_/_/package/0dfdb6daf30fb30a61d87d1f67c5e74535fe1679  
  
BOOST_INCLUDE=${BOOST_HOME}/include  
BOOST_LIB=${BOOST_HOME}/lib  
  
rm -f test.exe  
  
/usr/bin/x86_64-w64-mingw32-g++-posix \  
    -m64 \  
    -std=gnu++2a \  
    -D_GLIBCXX_USE_CXX11_ABI=1 \  
    test.cc \  
    -I${BOOST_INCLUDE} \  
    -L${BOOST_LIB} \  
    -lboost_coroutine -lboost_context  \  
    -lws2_32  \  
    -lwinpthread \  
    -static -static-libstdc++ -static-libgcc \  
    -fexceptions \  
    -o test.exe  
  
WINEPATH= wine ./test.exe  
```  
  
Running with Wine gives me an output:  
```shell  
$ wine ./test.exe   
terminate called after throwing an instance of 'std::runtime_error'  
  
abnormal program termination  
```  
  
Running in native Windows gives an output:  
```shell  
> test.exe  
16:error  
```  
  
My Linux station has the following software:  
```shell  
$ uname -a  
Linux vagrant 5.10.0-14-amd64 #1 SMP Debian 5.10.113-1 (2022-04-29) x86_64 GNU/Linux  
$ /usr/bin/x86_64-w64-mingw32-g++-posix --version  
x86_64-w64-mingw32-g++-posix (GCC) 10-posix 20210110  
$ dpkg -l | grep " g++-mingw-w64 "  
ii  g++-mingw-w64           10.2.1-6+24.2       all          GNU C++ compiler for MinGW-w64  
$ wine --version  
wine-5.0.3 (Debian 5.0.3-3)  
```  
  
My native windows is Windows 10.  
  
I build Boost lib from https://conan.io/center/boost recipe with MinGW.  
  
I see an important note in the documentation:  
  
> Windows using fcontext_t: turn off global program optimization (/GL) and change /EHsc (compiler  
> assumes that functions declared as extern "C" never throw a C++ exception) to /EHs (tells compiler  
> assumes that functions declared as extern "C" may throw an exception).  
  
and think it may be related to the problem I met. If so, can you, please, document the solution for MinGW compiler besides MSVC.

---

## Comment 1

> Username: ntff  
> Created at: 2022-07-04 09:16:26 UTC  
> Updated at: 2022-07-04 09:21:18 UTC  
> Url: https://github.com/boostorg/coroutine2/issues/45#issuecomment-1173568274  

I could build my executable for Win32, and it works being run with Wine32. Everything is quite the same, only target platform is different.  
This solution solves my task and Win64 problem is not a priority for me now.
