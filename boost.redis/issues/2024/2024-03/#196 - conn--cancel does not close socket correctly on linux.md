# #196 - conn->cancel does not close socket correctly on linux [Closed]

> Username: liuaifu  
> Created at: 2024-03-30 08:41:19 UTC  
> Updated at: 2024-06-03 13:16:08 UTC  
> Closed at: 2024-06-03 13:16:08 UTC  
> Url: https://github.com/boostorg/redis/issues/196  

On linux, socket is not closed correctly after calling conn->cancel, status changes to TIME_WAIT.  
  
    #include <boost/redis.hpp>  
    #include <boost/redis/src.hpp>  
    #include <boost/asio/deferred.hpp>  
    #include <boost/asio/co_spawn.hpp>  
    #include <boost/asio/detached.hpp>  
    #include <boost/asio/consign.hpp>  
    #include <boost/asio/strand.hpp>  
    #include <iostream>  
      
      
    namespace asio = boost::asio;  
    using boost::redis::request;  
    using boost::redis::response;  
    using boost::redis::config;  
    using boost::redis::connection;  
      
    // Called from the main function (see main.cpp)  
    auto co_main(config cfg) -> asio::awaitable<void>  
    {  
	    auto conn = std::make_shared<connection>(co_await asio::this_coro::executor);  
	    conn->async_run(cfg, {}, asio::consign(asio::detached, conn));  
      
	    // A request containing only a ping command.  
	    request req;  
	    req.push("PING", "Hello world");  
      
	    // Response where the PONG response will be stored.  
	    response<std::string> resp;  
      
	    // Executes the request.  
	    co_await conn->async_exec(req, resp, asio::deferred);  
	    conn->cancel();  
      
	    std::cout << "PING: " << std::get<0>(resp).value() << std::endl;  
    }  
      
    int main()  
    {  
	    try {  
		    config cfg;  
		    cfg.addr.host = "192.168.96.1";  
		    cfg.addr.port = "6379";  
      
		    asio::io_context ioc;  
		    // asio::io_context::work work(ioc);  
		    asio::co_spawn(asio::make_strand(ioc), co_main(cfg), [](std::exception_ptr p) {  
			    if (p)  
				    std::rethrow_exception(p);  
		    });  
		    ioc.run();  
      
	    }  
	    catch (std::exception const& e) {  
		    std::cerr << "(main) " << e.what() << std::endl;  
		    return 1;  
	    }  
    }  
  
The following are the results:  
  
    dev@LAPTOP-M2J7G4G3:~/redis-test/simple_test/build$ netstat -tpn  
    (No info could be read for "-p": geteuid()=1000 but you should be root.)  
    Active Internet connections (w/o servers)  
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name  
    dev@LAPTOP-M2J7G4G3:~/redis-test/simple_test/build$ ./simple-test  
    PING: Hello world  
    dev@LAPTOP-M2J7G4G3:~/redis-test/simple_test/build$ netstat -tpn  
    (No info could be read for "-p": geteuid()=1000 but you should be root.)  
    Active Internet connections (w/o servers)  
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name  
    tcp        0      0 192.168.96.27:54346     192.168.96.1:6379       TIME_WAIT   -  
    dev@LAPTOP-M2J7G4G3:~/redis-test/simple_test/build$  
  
test environment:  
1.Boost1.85beta1  
Redis-7.0.15-Windows-x64 run on Windows 11.  
app run on WSL2 + Ubuntu22.04  
  
2.Boost1.84  
Redis-7.0.15-Windows-x64 run on Windows 10.  
app run on Windows 10 + Virtualbox + Ubuntu22.04  
  
3.Boost1.85beta1  
Redis-7.0.15-Windows-x64 run on Windows 11.  
app compiled by VS2022，There is no problem in this case, the socket is closed correctly.  
  
Maybe this isn't boost.redis bug? Switching to hiredis has the same problem.

---

## Comment 1

> Username: mzimbres  
> Created at: 2024-03-30 20:04:31 UTC  
> Url: https://github.com/boostorg/redis/issues/196#issuecomment-2028456857  

Hi, calling `conn->cancel` results in the underlying tcp socket being closed locally and therefore it is notmal that it assumes the `TIME_WAIT` state. Why do you consider this to be a problem? Notice that  boost.redis implements multiplexing, that means you need only one connection in your whole app, it is therefore very unlikely that sockets in `TIME_WAIT` state will cause you any problem.  
  
You also have the option of not calling cancel but disabling reconnections and then executing a `QUIT` command. This is however not recomments because the server will have to close the socket itself, opening the door for fd exhaustion due to too many sockets inn `TIME_WAIT` state.

---

## Comment 2

> Username: liuaifu  
> Created at: 2024-03-31 13:41:40 UTC  
> Url: https://github.com/boostorg/redis/issues/196#issuecomment-2028746837  

> Hi, calling `conn->cancel` results in the underlying tcp socket being closed locally and therefore it is notmal that it assumes the `TIME_WAIT` state. Why do you consider this to be a problem?  
  
This is because this state persists for a short time after the program exits, and it is my understanding that SOCKET should be completely closed after the program exits, and the connection should no longer appear in netstat.

---

## Comment 3

> Username: mzimbres  
> Created at: 2024-03-31 16:53:05 UTC  
> Url: https://github.com/boostorg/redis/issues/196#issuecomment-2028821394  

What you are describing is completely normal snd expected.
