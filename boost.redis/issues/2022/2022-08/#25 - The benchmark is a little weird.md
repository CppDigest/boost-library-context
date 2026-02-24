# #25 - The benchmark is a little weird. [Closed]

> Username: Kidsunbo  
> Created at: 2022-08-25 07:44:45 UTC  
> Updated at: 2022-08-26 02:42:49 UTC  
> Closed at: 2022-08-26 02:42:49 UTC  
> Url: https://github.com/boostorg/redis/issues/25  

For Go version, you use `bufio` and not for C++ version.  
So I modify your code a little to give them a fair fight.  
  
C++ Server  
```c++  
//  
// echo_server.cpp  
// ~~~~~~~~~~~~~~~  
//  
// Copyright (c) 2003-2022 Christopher M. Kohlhoff (chris at kohlhoff dot com)  
//  
// Distributed under the Boost Software License, Version 1.0. (See accompanying  
// file LICENSE_1_0.txt or copy at http://www.boost.org/LICENSE_1_0.txt)  
//  
  
#include <cstdio>  
#include <boost/asio.hpp>  
  
namespace net = boost::asio;  
namespace this_coro = net::this_coro;  
using net::ip::tcp;  
using net::detached;  
using executor_type = net::io_context::executor_type;  
using socket_type = net::basic_stream_socket<net::ip::tcp, executor_type>;  
using tcp_socket = net::use_awaitable_t<executor_type>::as_default_on_t<socket_type>;  
using acceptor_type = net::basic_socket_acceptor<net::ip::tcp, executor_type>;  
using tcp_acceptor = net::use_awaitable_t<executor_type>::as_default_on_t<acceptor_type>;  
using awaitable_type = net::awaitable<void, executor_type>;  
constexpr net::use_awaitable_t<executor_type> use_awaitable;  
  
awaitable_type echo(tcp_socket socket)  
{  
    try {  
        char data[1024];  
        for (;;) {  
            std::size_t n = co_await socket.async_read_some(net::buffer(data), use_awaitable);  
            co_await async_write(socket, net::buffer(data, n), use_awaitable);  
        }  
    } catch (std::exception const& e) {  
        //std::printf("echo Exception: %s\n", e.what());  
    }  
}  
  
awaitable_type listener()  
{  
    auto ex = co_await this_coro::executor;  
    tcp_acceptor acceptor(ex, {tcp::v4(), 12345});  
    for (;;) {  
        tcp_socket socket = co_await acceptor.async_accept(use_awaitable);  
        co_spawn(ex, echo(std::move(socket)), detached);  
    }  
}  
  
int main()  
{  
    try {  
        net::io_context io_context{BOOST_ASIO_CONCURRENCY_HINT_UNSAFE_IO};  
        co_spawn(io_context, listener(), detached);  
        io_context.run();  
    } catch (std::exception const& e) {  
        std::printf("Exception: %s\n", e.what());  
    }  
}  
```  
  
Go server  
```go  
package main  
  
import (  
	"fmt"  
	"net"  
	"os"  
	"runtime"  
)  
  
func echo(conn net.Conn) {  
	buf := make([]byte, 1024)  
	for {  
		n, err := conn.Read(buf)  
		if err != nil {  
			break  
		}  
		_, err = conn.Write(buf[:n])  
		if err != nil {  
			break  
		}  
	}  
}  
  
func main() {  
	runtime.GOMAXPROCS(1)  
  
	l, err := net.Listen("tcp", "0.0.0.0:12345")  
	if err != nil {  
		fmt.Println("ERROR", err)  
		os.Exit(1)  
	}  
  
	for {  
		conn, err := l.Accept()  
		if err != nil {  
			fmt.Println("ERROR", err)  
			continue  
		}  
		go echo(conn)  
	}  
}  
  
```  
  
With a Go version client  
```go  
package main  
  
import (  
	"fmt"  
	"net"  
	"sync"  
	"sync/atomic"  
	"time"  
)  
  
const ConnNumber = 100  
const MessageLoop = 10000  
  
func main() {  
	start := time.Now()  
	wg := &sync.WaitGroup{}  
	wg.Add(ConnNumber)  
	for i := 0; i < ConnNumber; i++ {  
		go OneConn(wg)  
	}  
	wg.Wait()  
	fmt.Println("[TIME USED] ", time.Since(start).Milliseconds(), "[SUM] ", sum)  
}  
  
var sum int64 = 0  
  
func OneConn(wg *sync.WaitGroup) {  
	defer wg.Done()  
	conn, err := net.Dial("tcp", "127.0.0.1:12345")  
	if err != nil {  
		fmt.Println("Error: ", err)  
		return  
	}  
  
	for i := 0; i < MessageLoop; i++ {  
		n, err := conn.Write([]byte("PING\r\n"))  
		if err != nil {  
			fmt.Println("Error: ", err)  
			return  
		}  
		buf := make([]byte, 1024)  
		n, err = conn.Read(buf)  
		if err != nil {  
			fmt.Println("Error: ", err)  
			return  
		}  
		atomic.AddInt64(&sum, int64(n))  
	}  
}  
  
```  
  
On my computer, I run three loops for each version and here is the result:  
  
Go  
```  
[TIME USED]  7493 [SUM]  6000000  
[TIME USED]  7070 [SUM]  6000000  
[TIME USED]  7241 [SUM]  6000000  
```  
  
C++  
```  
[TIME USED]  7925 [SUM]  6000000  
[TIME USED]  7752 [SUM]  6000000  
[TIME USED]  7857 [SUM]  6000000  
```  
  
As the result shows, Go is a little faster than C++ with this simple IO task. Actually for IO task, Go is not that poor at performance .

---

## Comment 1

> Username: mzimbres  
> Created at: 2022-08-25 19:30:13 UTC  
> Url: https://github.com/boostorg/redis/issues/25#issuecomment-1227676515  

Great that you find that out, thanks. I have updated the graph and the benchmarks document. Go comes third now. Notice I use 1000 connections and 5000 messages in my benchmark (not 100 and 10000 as you have in your client). I also tried to fix  [echo_server_over_redis.go](https://github.com/mzimbres/aedis/blob/f11622e74678f9d1b9082bad103798871591f6d4/benchmarks/go/echo_server_over_redis.go) with your changes but it doesn't work somehow when benchmarking against a server I have in the cloud.

---

## Comment 2

> Username: Kidsunbo  
> Created at: 2022-08-26 02:42:49 UTC  
> Url: https://github.com/boostorg/redis/issues/25#issuecomment-1227981669  

That looks reasonable. Cheers.
