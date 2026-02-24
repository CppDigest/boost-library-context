# #1882 - Example of HTTP server, asynchronous doesn't compile [Closed]

> Username: iwahbe  
> Created at: 2020-03-20 05:36:29 UTC  
> Updated at: 2020-03-20 08:44:21 UTC  
> Closed at: 2020-03-20 08:44:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1882  

I am using the source code from: https://www.boost.org/doc/libs/1_66_0/libs/beast/example/http/server/async/http_server_async.cpp  
  
## Version of Beast  
I am using the install from boost: `1.72.0`  
I am using apple clang:  
  
```  
$ g++ --version  
Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX.sdk/usr/include/c++/4.2.1  
Apple clang version 11.0.0 (clang-1100.0.33.17)  
Target: x86_64-apple-darwin19.3.0  
Thread model: posix  
InstalledDir: /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain/usr/bin  
```  
Compiling with   
`g++ -std=c++17 example.cc`  
  
When I try to compile I get:  
```  
example.cc:233:37: error: no matching constructor for initialization of  
      'boost::asio::strand<boost::asio::io_context::executor_type>'  
      : socket_(std::move(socket)), strand_(socket_.get_executor()),  
                                    ^       ~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/include/boost/asio/strand.hpp:48:12: note: candidate constructor not viable: no known  
      conversion from 'boost::asio::basic_socket<boost::asio::ip::tcp,  
      boost::asio::executor>::executor_type' (aka 'boost::asio::executor') to 'const  
      boost::asio::io_context::executor_type' for 1st argument  
  explicit strand(const Executor& e)  
           ^  
/usr/local/include/boost/asio/strand.hpp:56:3: note: candidate constructor not viable: no known  
      conversion from 'boost::asio::basic_socket<boost::asio::ip::tcp,  
      boost::asio::executor>::executor_type' (aka 'boost::asio::executor') to 'const  
      boost::asio::strand<boost::asio::io_context::executor_type>' for 1st argument  
  strand(const strand& other) BOOST_ASIO_NOEXCEPT  
  ^  
/usr/local/include/boost/asio/strand.hpp:99:3: note: candidate constructor not viable: no known  
      conversion from 'boost::asio::basic_socket<boost::asio::ip::tcp,  
      boost::asio::executor>::executor_type' (aka 'boost::asio::executor') to  
      'boost::asio::strand<boost::asio::io_context::executor_type>' for 1st argument  
  strand(strand&& other) BOOST_ASIO_NOEXCEPT  
  ^  
/usr/local/include/boost/asio/strand.hpp:68:3: note: candidate template ignored: could not match  
      'strand<type-parameter-0-0>' against 'boost::asio::executor'  
  strand(  
  ^  
/usr/local/include/boost/asio/strand.hpp:111:3: note: candidate template ignored: could not match  
      'strand<type-parameter-0-0>' against 'boost::asio::executor'  
  strand(strand<OtherExecutor>&& other) BOOST_ASIO_NOEXCEPT  
  ^  
/usr/local/include/boost/asio/strand.hpp:40:3: note: candidate constructor not viable: requires 0  
      arguments, but 1 was provided  
  strand()  
  ^  
1 error generated.  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-03-20 08:20:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1882#issuecomment-601581950  

Hi @iwahbe ,  
  
The public API of Boost.Asio changed in the transition from Boost 1.69 to Boost 1.70.  
  
As of 1.70, the `executor_type` of the specialisation of `asio::ip::tcp::socket` became the polymorphic type `asio::executor`.  
  
It is still possible to create sockets (and other io objects) who's `executor_type` is `io_context::executor_type` but you would have to specialise the template `basic_socket` with the correct executor type:  
  
https://www.boost.org/doc/libs/1_72_0/doc/html/boost_asio/reference/basic_socket.html  
  
This breaking change in Asio is out of our control. We have taken care to update the examples in each release of Boost to ensure that the examples work with the same Boost version. That's the best we can do.  
  
Asio is changing because the c++ Net technical specification is evolving. Asio in large part reflects the state of the art.

---

## Comment 2

> Username: iwahbe  
> Created at: 2020-03-20 08:44:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1882#issuecomment-601589914  

Thanks for the amazingly quick response. The updated example works, as you probably knew. I'm not sure if this is the appropriate place, but it would make sense for the example to include boost version. The only place I see version for the examples is in the url, which is not a place I usually check thoroughly.   
Maybe something like   
```c++  
//------------------------------------------------------------------------------  
//  
// Example: HTTP server, asynchronous  
// Boost 72  
//------------------------------------------------------------------------------  
```
