# #10 - Assertion failed: top_!=0 && *top_==this, when using Boost::coroutine [Closed]

> Username: Tradias  
> Created at: 2020-10-04 12:24:33 UTC  
> Updated at: 2020-10-18 05:52:21 UTC  
> Closed at: 2020-10-18 05:52:20 UTC  
> Url: https://github.com/boostorg/leaf/issues/10  

The following code triggers assertion: `Assertion failed: top_!=0 && *top_==this, file boost/leaf/error.hpp, line 284`. Both on development branch from 4th October 2020 and in version 0.2.1.  
  
```c++  
#include <boost/asio.hpp>  
#include <boost/asio/spawn.hpp>  
#include <boost/leaf.hpp>  
  
namespace bl = boost::leaf;  
namespace asio = boost::asio;  
  
int main() {  
    asio::io_context io_context;  
    asio::spawn(io_context, [&](auto &&yield) {  
        bl::try_handle_some(  
            [&]() -> bl::result<void> {  
                asio::steady_timer timer(io_context);  
                timer.expires_after(std::chrono::milliseconds(100));  
                timer.async_wait(yield);  
                return bl::new_error(42);  
            },  
            [&](const int &) {});  
    });  
    asio::spawn(io_context, [&](auto &&yield) {  
        bl::try_handle_some(  
            [&]() -> bl::result<void> {  
                asio::steady_timer timer(io_context);  
                timer.expires_after(std::chrono::seconds(1));  
                timer.async_wait(yield);  
                return {};  
            },  
            [](const int &) {});  
    });  
    io_context.run();  
}  
```  
If the assertion doesn't trigger then you may want to increase the wait time of the first timer. The goal is to have the second `try_handle_some` function being called and stuck before the first one is called and has errored.  
  
To compile the code you will need Boost and link with the needed libraries:  
  
```cmake  
find_package(Boost 1.73 REQUIRED COMPONENTS coroutine)  
target_link_libraries(main PRIVATE Boost::boost Boost::coroutine)  
```  
  
I have tested it with Boost 1.73 and 1.74, but I am sure previous versions can be used as well.

---

## Comment 1

> Username: zajo  
> Created at: 2020-10-18 05:52:20 UTC  
> Url: https://github.com/boostorg/leaf/issues/10#issuecomment-711121170  

Coroutines are not supported at least for now. I'd be interested in a proof of concept implementation if that is possible.
