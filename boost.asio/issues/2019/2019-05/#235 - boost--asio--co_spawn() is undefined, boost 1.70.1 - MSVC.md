# #235 - boost::asio::co_spawn() is undefined, boost 1.70.1 - MSVC [Closed]

> Username: jtbry  
> Created at: 2019-05-08 22:24:50 UTC  
> Updated at: 2019-05-08 23:34:13 UTC  
> Closed at: 2019-05-08 23:34:13 UTC  
> Url: https://github.com/boostorg/asio/issues/235  

I am attempting to create a TCP server using `boost.asio` and following the examples you use [`co_spawn`](https://www.boost.org/doc/libs/1_70_0/doc/html/boost_asio/reference/co_spawn.html) to start the listener function in a new thread. When I try to use `boost::asio::co_spawn` Microsoft Visual Studios tells me it is undefined, I have included all files the examples do.  
  
TcpServer.h  
```cpp  
#include <iostream>  
#include <string>  
  
#include <boost/asio/co_spawn.hpp>  
#include <boost/asio/detached.hpp>  
#include <boost/asio/io_context.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <boost/asio/signal_set.hpp>  
#include <boost/asio/write.hpp>  
#include <boost/asio/coroutine.hpp>  
  
using namespace boost::asio;  
  
class TcpServer  
{  
public:  
	TcpServer(int port);  
	~TcpServer();  
private:  
	  
};  
```  
TcpServer.cpp  
```cpp  
#include "TcpServer.h"  
  
  
/*  
	Create a TcpServer on a given port  
*/  
TcpServer::TcpServer(int port)  
{  
	io_context ioCtx(1);  
  
	/* E0020	identifier "co_spawn" is undefined	*/  
	co_spawn();  
}  
  
  
TcpServer::~TcpServer()  
{  
}  
```

---

## Comment 1

> Username: jtbry  
> Created at: 2019-05-08 23:33:50 UTC  
> Updated at: 2019-05-08 23:34:08 UTC  
> Url: https://github.com/boostorg/asio/issues/235#issuecomment-490688737  

```  
boost.asio has preprocessor code to detect the capabilities of the compiler and only turn on coroutine support if the compiler supports it.  
  
For MSVC, I think you will need to add the compiler flag /await to the compiler command line option list.  
```  
[Answer ](https://stackoverflow.com/questions/56050105/boostasioco-spawn-is-undefined-in-msvc)Credit: [Richard Hodges](https://stackoverflow.com/users/2015579/richard-hodges)
