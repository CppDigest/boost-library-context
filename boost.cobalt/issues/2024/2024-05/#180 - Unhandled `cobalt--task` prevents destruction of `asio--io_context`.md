# #180 - Unhandled `cobalt::task` prevents destruction of `asio::io_context` [Open]

> Username: Lyulf  
> Created at: 2024-05-13 11:36:04 UTC  
> Updated at: 2024-05-13 11:40:50 UTC  
> Url: https://github.com/boostorg/cobalt/issues/180  

Code:  
```c++  
#include <cstdio>  
  
#include <boost/asio.hpp>  
#include <boost/cobalt.hpp>  
  
namespace asio = boost::asio;  
namespace cobalt = boost::cobalt;  
  
asio::awaitable<void> emptyAsioAwaitable()  
{  
  std::puts("Inside ASIO");  
  co_return;  
}  
  
cobalt::task<void> emptyCobaltTask()  
{  
  std::puts("Inside COBALT");  
  co_return;  
}  
  
void testCobalt()  
{  
  asio::io_context context;  
  cobalt::spawn(context, emptyCobaltTask(), asio::detached);  
  context.stop();  
}  
  
void testAsio()  
{  
  asio::io_context context;  
  asio::co_spawn(context, emptyAsioAwaitable(), asio::detached);  
  context.stop();  
}  
  
int main()  
{  
  std::puts("before ASIO test");  
  testAsio();  
  std::puts("after ASIO test");  
  
  std::puts("before COBALT test");  
  testCobalt();  
  std::puts("after COBALT test");  
  return 0;  
}  
  
```  
  
Built on Windows 10 64-bit using MSVC 17.9.6 (cl version 19.39.33523 for x64) with following command:  
```  
cl /Femain.exe main.cpp /nologo /external:IC:\Users\chodorowskip\repo\deps\vc143\boost-1.85.0\include\boost-1_85 /std:c++20 /MD /Z7 /O2 /EHsc /D_WIN32_WINNT=0x0601 /W4 /external:W0 /link /PROFILE /LIBPATH:C:\Users\chodorowskip\repo\deps\vc143\boost-1.85.0\lib libboost_cobalt-vc143-mt-x64-1_85.lib  
```  
  
Gives following result:  
```  
before ASIO test  
after ASIO test  
before COBALT test  
```

---

## Comment 1

> Username: Pele44  
> Created at: 2024-05-13 11:40:18 UTC  
> Updated at: 2024-05-13 11:40:50 UTC  
> Url: https://github.com/boostorg/cobalt/issues/180#issuecomment-2107350070  

With gcc 13.2 and clang 17.0.6, running under Fedora 39 (x86_64), problem doesn't occur. But running under Windows, example built by MSVC 19.39 get's stuck on io_context dcor...
