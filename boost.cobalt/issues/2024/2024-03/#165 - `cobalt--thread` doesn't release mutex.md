# #165 - `cobalt::thread` doesn't release mutex [Open]

> Username: Lyulf  
> Created at: 2024-03-22 07:24:10 UTC  
> Updated at: 2024-04-01 23:11:50 UTC  
> Url: https://github.com/boostorg/cobalt/issues/165  

Following code:  
  
```  
#include <boost/cobalt.hpp>  
  
namespace cobalt = boost::cobalt;  
  
cobalt::thread emptyThread()  
{  
  co_return;  
}  
  
int main(int /*argc*/, char** /*argv*/)  
{  
  auto thread = emptyThread();  
  thread.join();  
  return 0;  
}  
```  
  
Built on Windows 10 64-bit using MSVC 17.9.0 (cl 19.39.33519 for x64) with following command:  
`cl /Femain.exe main.cpp /std:c++20 /MDd /EHsc -D_WIN32_WINNT=0x0601 /external:W0 /external:IC:\Users\chodorowskip\repo\deps\vc143\boost-1.84.0\include\boost-1_84 /link /DEBUG /LIBPATH:C:\Users\chodorowskip\repo\deps\vc143\boost-1.84.0\lib libboost_cobalt-vc143-mt-gd-x64-1_84.lib`  
  
Call aborts and gives following error message in console:  
`D:\a\_work\1\s\src\vctools\crt\github\stl\src\mutex.cpp(49): mutex destroyed while busy`  
  
This error doesn't seem to happen when thread is suspended at least once. For example using the following code:  
  
```  
#include <coroutine>  
  
#include <boost/asio.hpp>  
#include <boost/cobalt.hpp>  
  
namespace asio = boost::asio;  
namespace cobalt = boost::cobalt;  
  
struct Awaitable  
{  
  bool await_ready()  
  {  
    return false;  
  }  
  template <typename Promise>  
  void await_suspend(std::coroutine_handle<Promise> handle)  
  {  
    auto executor = handle.promise().get_executor();  
    auto h = cobalt::unique_handle<void>(handle.address());  
    asio::post(executor, std::move(h));  
  }  
  void await_resume() {}  
};  
  
cobalt::thread emptyThread()  
{  
  co_await Awaitable{};  
}  
  
int main(int /*argc*/, char** /*argv*/)  
{  
  auto thread = emptyThread();  
  thread.join();  
  return 0;  
}  
```
