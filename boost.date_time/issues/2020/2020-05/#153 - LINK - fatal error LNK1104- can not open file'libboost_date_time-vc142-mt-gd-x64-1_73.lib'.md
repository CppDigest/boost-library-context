# #153 - LINK : fatal error LNK1104: can not open file'libboost_date_time-vc142-mt-gd-x64-1_73.lib' [Closed]

> Username: Catutigernos  
> Created at: 2020-05-12 08:18:29 UTC  
> Updated at: 2020-07-22 15:32:18 UTC  
> Closed at: 2020-07-22 15:32:18 UTC  
> Url: https://github.com/boostorg/date_time/issues/153  

Hi,  
It is my first time with boost libraries. I am trying to learn, so I download all boost libraries and build. It is successfully built, and I added include and linkers library path to cmake file. I am working on asio, asio code work fine, but when I used timer, compiler give the error.  I checked file exists, I search on Internet, find some solutions, but they did not work. I looked precompiled files, but there is no any file for boost 1.7.3.  Operating system is Windows 10 and I am working with Visual Studio Code and CMake. Is there any chance that date_time library is not built correctly? and How can I solve this problem?  
Thanks in advance.  
  
  
Working code:  
```  
#define _WIN32_WINNT  0x0A00   
#include <iostream>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/post.hpp>  
using namespace std;  
  
int main()  
{  
  boost::asio::io_context io_context;  
  boost::asio::post(io_context, []() { cout<<"test"<<endl; });  
  for (;;)  
  {  
    try  
    {  
  
      io_context.run();  
      break;   
    }  
    catch (exception& e)  
    {  
      cout<<e.what()<<endl;  
    }  
  }  
  int open;  
  std::cin>>open;  
  return 0;  
}  
```  
  
Compiler Error Code:  
  
```  
#define _WIN32_WINNT  0x0A00   
#include <boost/asio.hpp>  
#include <boost/bind.hpp>  
#include <boost/date_time/posix_time/posix_time.hpp>  
#include <iostream>  
  
void print(const boost::system::error_code&, boost::asio::deadline_timer* t,int* count)  
{  
    if (*count < 5)  
    {  
        std::cout << *count << std::endl;  
        ++(*count);  
        t->expires_from_now(boost::posix_time::seconds(1));  
        t->async_wait(boost::bind(print, boost::asio::placeholders::error, t, count));  
    }  
}  
  
int main()  
{   
    boost::asio::io_service io;  
    int count = 0;  
    boost::asio::deadline_timer t(io, boost::posix_time::seconds(1));  
  
    t.async_wait(boost::bind(print, boost::asio::placeholders::error, &t, &count));  
    io.run();  
    std::cout << "Final count is " << count << std::endl;  
    return 0;  
  
}  
```  
  
Compiler Error:  
  
> LINK : fatal error LNK1104: can not open file'libboost_date_time-vc142-mt-gd-x64-1_73.lib'  
  
CmakeLists.txt file:  
  
```  
cmake_minimum_required(VERSION 3.0.0)  
SET(BOOST_INCLUDE  D:/libraries/boost_1_73_0)  
SET(BOOST_LIB D:/libraries/boost_1_73_0/stage/lib)  
project(boost-test VERSION 1.0)  
  
set(EXEC main)  
set(CMAKE_CXX_STANDARD 20)  
set(CMAKE_CXX_STANDARD_REQUIRED ON)  
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/include ${BOOST_INCLUDE})  
file(GLOB SOURCES src/*.cpp)  
add_executable(${EXEC} ${SOURCES})  
target_link_libraries(${EXEC} ${BOOST_LIB})  
```

---

## Comment 1

> Username: Catutigernos  
> Created at: 2020-05-12 10:09:17 UTC  
> Url: https://github.com/boostorg/date_time/issues/153#issuecomment-627245951  

I solved problem. I add   
  
> ADD_DEFINITIONS("-DBOOST_ALL_NO_LIB")  
  
to CMake file and the code works correctly.

---

## Comment 2

> Username: JeffGarland  
> Created at: 2020-05-12 15:53:26 UTC  
> Url: https://github.com/boostorg/date_time/issues/153#issuecomment-627432024  

hmm, with 1.73 date_time should never require a library (regardless of BOOST_ALL_NO_LIB) although a stub library is maintained for backward compatibility.  This error makes me think you didn't get the stub library built?  
  
    LINK : fatal error LNK1104: can not open file'libboost_date_time-vc142-mt-gd-x64-1_73.lib'

---

## Comment 3

> Username: Catutigernos  
> Created at: 2020-05-14 07:06:46 UTC  
> Url: https://github.com/boostorg/date_time/issues/153#issuecomment-628434196  

Hi Jeff,  
I downloaded all project and built it. Logically if there was any missing library which was not built, then I could not use library. My test program works fine. I dont know exactly architecture, but I think timer needs date_time library, so timer works fine. How I solved problem, I have no idea, I just solved. It is all luck. I was searching on Internet for solutions and I found solutions for date_time library does not works, so I searched similar issue for other boost libraries and this is only soluiton which works.

---

## Comment 4

> Username: JeffGarland  
> Created at: 2020-05-14 14:13:42 UTC  
> Url: https://github.com/boostorg/date_time/issues/153#issuecomment-628664205  

Ok,understood. So yes, this line of code:  
  
        t->expires_from_now(boost::posix_time::seconds(1));  
  
boost::posix_time::seconds is from boost date_time.  Which doesn't require you to have a linked library because the code is all inlined.  So there's something about the cmake setup that 'assumes' you need the library and that is probably incorrect.  But still, when you built boost you should have a boost_1_73/stage directory that had the library you're not able to find above.    
  
Anyway I look into this a bit more bc I can see at least one other place that asio assumes date_time library is needed and get that cleaned up.  Otherwise, I'm going to close this out since your issue is resolved.
