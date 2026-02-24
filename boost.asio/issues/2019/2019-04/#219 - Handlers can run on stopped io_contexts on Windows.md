# #219 - Handlers can run on stopped io_contexts on Windows [Closed]

> Username: Marc-Aldorasi-Imprivata  
> Created at: 2019-04-04 20:32:34 UTC  
> Updated at: 2020-12-30 01:02:38 UTC  
> Closed at: 2020-12-30 01:02:37 UTC  
> Url: https://github.com/boostorg/asio/issues/219  

When running the following program on Windows:  
```c++  
#include <iostream>  
#include <boost/asio.hpp>  
int main() {  
	::boost::asio::io_context ctx(1);  
	int handler_called = 0;  
	auto handler = [&] { ++handler_called; };  
	std::cout << handler_called << ' ' << ctx.stopped() << std::endl;  
	ctx.poll();  
	std::cout << handler_called << ' ' << ctx.stopped() << std::endl;  
	ctx.get_executor().post(handler, std::allocator<void>{});  
	ctx.poll();  
	std::cout << handler_called << ' ' << ctx.stopped() << std::endl;  
	ctx.poll();  
	std::cout << handler_called << ' ' << ctx.stopped() << std::endl;  
}  
```  
it prints  
```  
0 0  
0 1  
0 1  
1 1  
```  
  
This indicates that the first call to `poll` causes the context to be stopped, and the third call to `poll` causes the handler to execute.  This is contrary to the description of `stopped`, which says "When an io_context object is stopped, calls to run(), run_one(), poll() or poll_one() will return immediately without invoking any handlers".  This occurs when using mingw and MSVC, but does not occur on Linux.

---

## Comment 1

> Username: ghost  
> Created at: 2020-12-30 01:02:35 UTC  
> Url: https://github.com/boostorg/asio/issues/219#issuecomment-752291736  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#680](https://github.com/chriskohlhoff/asio/issues/680).
