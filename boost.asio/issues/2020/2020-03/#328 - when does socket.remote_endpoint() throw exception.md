# #328 - when does socket.remote_endpoint() throw exception? [Closed]

> Username: caromdreamer  
> Created at: 2020-03-03 17:47:12 UTC  
> Updated at: 2022-03-11 06:46:55 UTC  
> Closed at: 2020-03-03 18:34:11 UTC  
> Url: https://github.com/boostorg/asio/issues/328  

I got core dump of my program.  
```  
#0  0x0000003e8c0324f5 in raise (sig=6) at ../nptl/sysdeps/unix/sysv/linux/raise.c:64  
#1  0x0000003e8c033cd5 in abort () at abort.c:92  
#2  0x0000003cffcbea8d in __gnu_cxx::__verbose_terminate_handler() () from /usr/lib64/libstdc++.so.6  
#3  0x0000003cffcbcbe6 in ?? () from /usr/lib64/libstdc++.so.6  
#4  0x0000003cffcbcc13 in std::terminate() () from /usr/lib64/libstdc++.so.6  
#5  0x0000003cffcbcd32 in __cxa_throw () from /usr/lib64/libstdc++.so.6  
#6  0x000000000043a6e0 in boost::throw_exception<boost::system::system_error> (e=...) at /home/neo-bridge/NeoBridge/neo-bridge-cpp/boost_output/include/boost/throw_exception.hpp:70  
#7  0x000000000040eef1 in boost::asio::detail::do_throw_error (err=..., location=0x8fec8c "remote_endpoint")  
    at /home/neo-bridge/NeoBridge/neo-bridge-cpp/boost_output/include/boost/asio/detail/impl/throw_error.ipp:38  
#8  0x000000000040ec2f in boost::asio::detail::throw_error (err=..., location=0x8fec8c "remote_endpoint") at /home/neo-bridge/NeoBridge/neo-bridge-cpp/boost_output/include/boost/asio/detail/throw_error.hpp:42   
#9  0x000000000044664c in boost::asio::basic_socket<boost::asio::ip::tcp, boost::asio::executor>::remote_endpoint (this=0x7fff26f02dc0)  
    at /home/neo-bridge/NeoBridge/neo-bridge-cpp/boost_output/include/boost/asio/basic_socket.hpp:1609  
#10 0x0000000000439d49 in NoSSLListener::on_accept (this=0x2376440, ec=..., socket=...) at /home/neo-bridge/NeoBridge/neo-bridge-cpp/src/main.cpp:167  
```  
  
Crash in remote_endpoint function in boost :: asio.  
Above is the call stack of the core dump in case of error.  
  
The # 10 and # 9 parts are as follows.  
```   
/home/neo-bridge/NeoBridge/neo-bridge-cpp/boost_output/include/boost/asio/detail/throw_error.hpp:42   
 38 inline void throw_error(const boost::system::error_code& err,                                         
 39     const char* location)                                                                             
 40 {                                                                                                     
 41   if (err)                                                                                            
 42       do_throw_error(err, location);  
/home/neo-bridge/NeoBridge/neo-bridge-cpp/boost_output/include/boost/asio/basic_socket.hpp:1609  
|1604   endpoint_type remote_endpoint() const  
|1605   {  
|1606     boost::system::error_code ec;  
|1607     endpoint_type ep = impl_.get_service().remote_endpoint(  
|1608         impl_.get_implementation(), ec);  
|1609     boost::asio::detail::throw_error(ec, "remote_endpoint");  
|1610     return ep;  
|1611   }  
```  
Eventually, remote_endpoint function throws error_code into throw_error and does do_throw_error.  
```  
 63 template<class E> BOOST_NORETURN inline void throw_exception( E const & e )   
 64 {   
 65     //All boost exceptions are required to derive from std::exception,   
 66     //to ensure compatibility with BOOST_NO_EXCEPTIONS.   
 67     throw_exception_assert_compatibility(e);   
 68    
 69 #ifndef BOOST_EXCEPTION_DISABLE   
 70     throw exception_detail::enable_both( e );  
```  
Finally, the throw is executed, resulting in an Abort.  
The code in question is: onAccept MIDDLE logs are aborted without being printed.  
When does remote_endpoint () give an error? Is there a way to avoid the crash?  
  
```  
main.cpp  
  160         void on_accept(beast::error_code ec, tcp::socket socket) {  
  161             if(ec) {  
  162                 fail(ec, "accept");  
  163             } else {  
  164                 // Create the session and run it  
  165                 CONSOLE_INFO("[TCP] onAccept BEGIN");  
  166   
  167                 // auto& test = socket;  
  168                 auto t = socket.remote_endpoint().address().to_string();  
  169                 boost::asio::ip::tcp::no_delay option(true);  
  170                 socket.set_option(option);  
  171                 CONSOLE_INFO("[TCP] onAccept MIDDLE");  
```  
  
That exception is very rare. It's really rare that it's hard to provide sample code.  
  
If I surround my code with try, will it work on the next accept?

---

## Comment 1

> Username: SiyiJia  
> Created at: 2022-03-01 22:14:41 UTC  
> Url: https://github.com/boostorg/asio/issues/328#issuecomment-1055911899  

Hi, I had the same issue. Have you found the reason?

---

## Comment 2

> Username: caromdreamer  
> Created at: 2022-03-11 06:46:55 UTC  
> Url: https://github.com/boostorg/asio/issues/328#issuecomment-1064823501  

What is your boost version? I remember using below boost 1.72.  
  
I upgraded boost version then I believe its problem fixed.
