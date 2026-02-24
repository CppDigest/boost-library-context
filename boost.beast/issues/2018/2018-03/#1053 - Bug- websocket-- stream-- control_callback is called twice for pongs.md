# #1053 - Bug: websocket:: stream:: control_callback is called twice for pongs [Closed]

> Username: bebuch  
> Created at: 2018-03-01 18:48:59 UTC  
> Updated at: 2018-03-02 09:49:12 UTC  
> Closed at: 2018-03-01 20:29:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1053  

### Branch develop, commit 2543dbe5b93a6fe91a8f10e3c432b694ba785823  
  
Minimal example:  
  
```  
#include <boost/beast/core.hpp>  
#include <boost/beast/websocket.hpp>  
#include <boost/asio/connect.hpp>  
#include <boost/asio/ip/tcp.hpp>  
#include <cstdlib>  
#include <functional>  
#include <iostream>  
#include <string>  
#include <future>  
  
using tcp = boost::asio::ip::tcp;  
namespace websocket = boost::beast::websocket;  
  
  
int main(){  
    try  
    {  
        auto const host = "127.0.0.1";  
        auto const address = boost::asio::ip::make_address(host);  
        auto const port = 1234;  
  
        boost::asio::io_context server_ioc{1};  
  
        tcp::acceptor acceptor{server_ioc, {address, port}};  
  
  
        auto ping = std::async(std::launch::async, [&]{  
            tcp::socket server_socket{server_ioc};  
  
            auto accept_connect = std::async(std::launch::async, [&]{  
                acceptor.accept(server_socket);  
            });  
  
            boost::asio::io_context client_ioc;  
            tcp::resolver resolver{client_ioc};  
            websocket::stream<tcp::socket> client_ws{client_ioc};  
  
            auto const results = resolver.resolve(host, std::to_string(port));  
  
            boost::asio::connect(client_ws.next_layer(), results.begin(), results.end());  
            accept_connect.get();  
  
            websocket::stream<tcp::socket> server_ws{std::move(server_socket)};  
  
            auto accept_handshake = std::async(std::launch::async, [&]{  
                server_ws.accept();  
            });  
  
            client_ws.handshake(host, "/");  
            accept_handshake.get();  
  
            client_ws.control_callback(  
                [](  
                    boost::beast::websocket::frame_type kind,  
                    boost::beast::string_view  
                ){  
                    if(kind == boost::beast::websocket::frame_type::pong){  
                        std::cout << "pong\n";  
                    }  
                });  
  
            server_ws.control_callback(  
                [&server_ws](  
                    boost::beast::websocket::frame_type kind,  
                    boost::beast::string_view  
                ){  
                    if(kind == boost::beast::websocket::frame_type::ping){  
                        std::cout << "ping\n";  
                        server_ws.pong({});  
                    }  
                });  
  
            client_ws.ping({});  
  
            auto server_read = std::async(std::launch::async, [&]{  
                boost::beast::multi_buffer buffer;  
                server_ws.read(buffer);  
            });  
  
            auto client_read = std::async(std::launch::async, [&]{  
                boost::beast::multi_buffer buffer;  
                client_ws.read(buffer);  
            });  
  
            server_read.get();  
            client_read.get();  
        });  
    }  
    catch (const std::exception& e)  
    {  
        std::cerr << "Error: " << e.what() << std::endl;  
        return EXIT_FAILURE;  
    }  
}  
```  
  
Output:  
  
```  
ping  
pong  
pong  
```  
  
Expected output:  
  
```  
ping  
pong  
```  
  
Compile via bjam (place `Jamroot.jam` in the same dir as the .cpp file)  
  
```  
import os ;  
local boost = [ os.environ BOOST ] ;  
  
use-project /boost  
	: $(boost)  
	;  
  
exe main  
	:  
	[ glob *.cpp ]  
	/boost//system  
	:  
	<define>BOOST_ASIO_HAS_STD_CHRONO  
  
	<toolset>gcc:<cxxflags>-std=c++14  
	<toolset>gcc:<linkflags>-lpthread  
  
	<toolset>clang:<cxxflags>-std=c++14  
	<toolset>clang:<cxxflags>-stdlib=libc++  
	<toolset>clang:<cxxflags>-DBOOST_ASIO_HAS_STD_CHRONO  
	<toolset>clang:<linkflags>-lpthread  
	<toolset>clang:<linkflags>-lc++abi  
	<toolset>clang:<linkflags>-stdlib=libc++  
  
	<include>$(boost)  
	;  
```  
  
and call on command line one of:  
  
```  
$ bjam toolset=gcc  
$ bjam toolset=clang  
```  
  
Tested on Ubuntu 16.04 with:  
  
```  
$ g++ --version  
g++ (GCC) 8.0.1 20180223 (experimental)  
Copyright (C) 2018 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
$ g++-6 --version  
g++-6 (Ubuntu/Linaro 6.3.0-18ubuntu2~16.04) 6.3.0 20170519  
Copyright (C) 2016 Free Software Foundation, Inc.  
This is free software; see the source for copying conditions.  There is NO  
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  
  
$ clang++ --version  
clang version 6.0.0 (http://llvm.org/git/clang.git 4a005620928c39874a0177ec97d84e636ab8dbfc) (http://llvm.org/git/llvm.git e9667680470d3c40c51ff887fd33c2a91a07d62f)  
Target: x86_64-unknown-linux-gnu  
Thread model: posix  
InstalledDir: /home/bebuch/media/programme/llvm/bin  
```

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-03-01 18:57:20 UTC  
> Updated at: 2018-03-01 18:57:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1053#issuecomment-369693701  

Thank you for the detailed report. I was able to compile and run your sample application (Visual Studio 2017 and Windows 10), and I get the following output:  
```  
ping  
pong  
pong  
```  
Investigating...

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-03-01 19:22:26 UTC  
> Updated at: 2018-03-01 19:22:36 UTC  
> Url: https://github.com/boostorg/beast/issues/1053#issuecomment-369701073  

This is not a defect. `websocket::stream` automatically responds to a ping with a pong. It is not necessary for the user to call `pong` or `async_pong` to handle this case. You are calling `server_ws.pong()` in the control callback, which results in an extra message. The documentation is clear on this:  
  
_"The implementation will respond to pings automatically."_  
http://www.boost.org/doc/libs/1_66_0/libs/beast/doc/html/beast/using_websocket/control_frames.html

---

## Comment 3

> Username: bebuch  
> Created at: 2018-03-02 09:49:12 UTC  
> Url: https://github.com/boostorg/beast/issues/1053#issuecomment-369874958  

A few days ago I was looking for such a hint in the documentation, but I couldn't find it then. Your commit 46554b031a25bb945a97fece91ff0bbce95a8afd should fix this, the reference to control_callback is now clear and easy to find. Thank you very much! :-)
