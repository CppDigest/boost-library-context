# #1126 - ASIO/Beast Header Compile Errors [Closed]

> Username: ghost  
> Created at: 2018-05-14 01:12:20 UTC  
> Updated at: 2018-05-14 14:57:04 UTC  
> Closed at: 2018-05-14 14:57:04 UTC  
> Url: https://github.com/boostorg/beast/issues/1126  

### Version of Beast  
  
`#define BOOST_BEAST_VERSION 144`  
Boost 1.66 (Built from source on Mac OS X)  
  
### Steps necessary to reproduce the problem  
  
Header files are producing compile errors:  
  
<img width="509" alt="screen shot 2018-05-13 at 8 03 30 pm" src="https://user-images.githubusercontent.com/16402451/39973725-c0071bf8-56e8-11e8-8357-b06dcda03db4.png">  
  
<img width="646" alt="screen shot 2018-05-13 at 8 03 36 pm" src="https://user-images.githubusercontent.com/16402451/39973729-c9ccac0c-56e8-11e8-892b-32ed63041a80.png">   
  
### All relevant compiler information  
  
Cmake command: `cmake -DCMAKE_BUILD_TYPE=Debug -G "CodeBlocks - Unix Makefiles" $PROJECT_DIR`  
  
Cmake version 3.1  
  
Clang version:  
  
<img width="804" alt="screen shot 2018-05-13 at 8 10 07 pm" src="https://user-images.githubusercontent.com/16402451/39973842-d2f5044a-56e9-11e8-90b1-86c0a9154b00.png">  
  
### Possibly related topics  
  
https://github.com/boostorg/beast/issues/799 - It mentions problems with `boost::system::error_code`, might be related?  
  
https://github.com/boostorg/beast/issues/252 - Mentions compile errors

---

## Comment 1

> Username: vinniefalco  
> Created at: 2018-05-14 01:25:46 UTC  
> Updated at: 2018-05-14 01:26:53 UTC  
> Url: https://github.com/boostorg/beast/issues/1126#issuecomment-388672327  

`error_code` comes from here:  
  
https://github.com/boostorg/beast/blob/boost-1.66.0/include/boost/beast/core/error.hpp#L20  
  
`<boost/beast/websocket/stream.hpp>` includes `<boost/beast/websocket/error.hpp>` which includes `<boost/beast/core/error.hpp>` so I'm not sure why you aren't seeing the declaration.  
  
I would double check and make sure that the boost headers which you are including, are coming from the directory that they should be coming from and not a different directory.  
  
Anyway, could you please include the full text of the error message emitted by the compiler (instead of a tooltip)?  
  
Thanks

---

## Comment 2

> Username: ghost  
> Created at: 2018-05-14 01:30:48 UTC  
> Url: https://github.com/boostorg/beast/issues/1126#issuecomment-388672837  

I'm following the example here: https://github.com/boostorg/beast/blob/develop/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp  
  
There is just one error message from the compiler:  
  
```  
error: no member named 'async_handshake' in 'boost::asio::basic_stream_socket<boost::asio::ip::tcp>'  
        ws_.next_layer().async_handshake(ssl::stream_base::client,  
```  
  
Pointing to this line of code:  
  
<img width="679" alt="screen shot 2018-05-13 at 8 29 19 pm" src="https://user-images.githubusercontent.com/16402451/39974149-5a5c6d90-56ec-11e8-804f-68b5a4a502c2.png">  
  
I had similar problems before while following the example and it was due to other header files not compiling properly. I'd presume this case is the same, but I could be wrong

---

## Comment 3

> Username: ghost  
> Created at: 2018-05-14 01:34:20 UTC  
> Url: https://github.com/boostorg/beast/issues/1126#issuecomment-388673179  

The compiler is only not seeing the declaration when `error_code` is enclosed within `void()`. For example, in the same file it is visible in a function argument. Perhaps its a compiler version problem?  
  
<img width="579" alt="screen shot 2018-05-13 at 8 32 46 pm" src="https://user-images.githubusercontent.com/16402451/39974257-00d1fde8-56ed-11e8-9800-bf7fcfd28f6f.png">

---

## Comment 4

> Username: ghost  
> Created at: 2018-05-14 01:43:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1126#issuecomment-388674231  

```  
I would double check and make sure that the boost headers which you are including, are coming from the directory that they should be coming from and not a different directory.  
```  
  
You were right, my Cmake's `find_package` was including the old boost headers. But `ws_.next_layer().async_handshake` still isn't within the new headers. Thoughts?  
  
<img width="695" alt="screen shot 2018-05-13 at 8 42 59 pm" src="https://user-images.githubusercontent.com/16402451/39974429-5041008a-56ee-11e8-85e2-fddf77efd34c.png">

---

## Comment 5

> Username: vinniefalco  
> Created at: 2018-05-14 01:48:40 UTC  
> Updated at: 2018-05-14 01:48:54 UTC  
> Url: https://github.com/boostorg/beast/issues/1126#issuecomment-388674832  

What is the type of `ws_`? Please include the full text of the error message emitted by the compiler (instead of a tooltip)...It says "async_connect" is ambiguous. Where's the rest of the error output?

---

## Comment 6

> Username: ghost  
> Created at: 2018-05-14 02:03:30 UTC  
> Updated at: 2018-05-14 02:05:07 UTC  
> Url: https://github.com/boostorg/beast/issues/1126#issuecomment-388676604  

You were right, I incorrectly specified the type of `ws_`. I fixed it.  
  
The ambiguous call still gives an error message:  
  
[output.txt](https://github.com/boostorg/beast/files/1999181/output.txt)

---

## Comment 7

> Username: vinniefalco  
> Created at: 2018-05-14 02:07:03 UTC  
> Url: https://github.com/boostorg/beast/issues/1126#issuecomment-388677040  

Scanning dependencies of target beast_example  
[ 50%] Building CXX object CMakeFiles/beast_example.dir/main.cpp.o  
/Users/arminnaderi/CLionProjects/beast-example/main.cpp:60:9: error: no matching function for call to 'async_connect'  
        boost::asio::async_connect(ws_.next_layer(), results.begin(), results.end(),  
        ^~~~~~~~~~~~~~~~~~~~~~~~~~  
/usr/local/Mellar/boost/1_66_0/include/boost/asio/impl/connect.hpp:699:1: note: candidate template ignored: could not match 'basic_socket' against 'stream'  
async_connect(basic_socket<Protocol BOOST_ASIO_SVC_TARG>& s,  
^  
/usr/local/Mellar/boost/1_66_0/include/boost/asio/impl/connect.hpp:729:1: note: candidate template ignored: could not match 'basic_socket' against 'stream'  
async_connect(basic_socket<Protocol BOOST_ASIO_SVC_TARG>& s,  
^  
/usr/local/Mellar/boost/1_66_0/include/boost/asio/impl/connect.hpp:755:1: note: candidate template ignored: could not match 'basic_socket' against 'stream'  
async_connect(basic_socket<Protocol BOOST_ASIO_SVC_TARG>& s,  
^  
/usr/local/Mellar/boost/1_66_0/include/boost/asio/impl/connect.hpp:780:1: note: candidate template ignored: could not match 'basic_socket' against 'stream'  
async_connect(basic_socket<Protocol BOOST_ASIO_SVC_TARG>& s,  
^  
/usr/local/Mellar/boost/1_66_0/include/boost/asio/impl/connect.hpp:809:1: note: candidate template ignored: could not match 'basic_socket' against 'stream'  
async_connect(basic_socket<Protocol BOOST_ASIO_SVC_TARG>& s,  
^  
/usr/local/Mellar/boost/1_66_0/include/boost/asio/impl/connect.hpp:836:1: note: candidate function template not viable: requires 5 arguments, but 4 were provided  
async_connect(basic_socket<Protocol BOOST_ASIO_SVC_TARG>& s,  
^  
In file included from /Users/arminnaderi/CLionProjects/beast-example/main.cpp:4:  
In file included from /usr/local/Mellar/boost/1_66_0/include/boost/beast/websocket.hpp:18:  
In file included from /usr/local/Mellar/boost/1_66_0/include/boost/beast/websocket/stream.hpp:3455:  
/usr/local/Mellar/boost/1_66_0/include/boost/beast/websocket/impl/stream.ipp:47:7: error: no matching constructor for initialization of 'boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >'  
    : stream_(std::forward<Args>(args)...)  
      ^       ~~~~~~~~~~~~~~~~~~~~~~~~  
/Users/arminnaderi/CLionProjects/beast-example/main.cpp:35:70: note: in instantiation of function template specialization 'boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> > >::stream<boost::asio::io_context &>' requested here  
    explicit session(boost::asio::io_context& ioc) : resolver_(ioc), ws_(ioc) {}  
                                                                     ^  
/usr/local/Mellar/boost/1_66_0/include/boost/asio/ssl/stream.hpp:64:7: note: candidate constructor (the implicit copy constructor) not viable: no known conversion from 'boost::asio::io_context' to 'const boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >' for 1st argument  
class stream :  
      ^  
/usr/local/Mellar/boost/1_66_0/include/boost/asio/ssl/stream.hpp:98:3: note: candidate constructor template not viable: requires 2 arguments, but 1 was provided  
  stream(Arg&& arg, context& ctx)  
  ^  
2 errors generated.  
make[2]: *** [CMakeFiles/beast_example.dir/main.cpp.o] Error 1  
make[1]: *** [CMakeFiles/beast_example.dir/all] Error 2  
make: *** [all] Error 2

---

## Comment 8

> Username: vinniefalco  
> Created at: 2018-05-14 02:10:33 UTC  
> Updated at: 2018-05-14 02:11:40 UTC  
> Url: https://github.com/boostorg/beast/issues/1126#issuecomment-388677463  

1. `ws_.next_layer()` is of type `ssl::stream`, but `boost::asio::async_connect` expects a `basic_socket`. You need to pass `ws_.next_layer().next_layer()` instead.  
  
2. The `ssl::stream` constructor requires two arguments: the `io_context`, and the `ssl::context`.  
  
You might want to have a look at the asynchronous ssl websocket client example for tips:  
  
https://github.com/boostorg/beast/blob/344d957f4260bff5ef9c511e18e5060c5ace2fa4/example/websocket/client/async-ssl/websocket_client_async_ssl.cpp#L92

---

## Comment 9

> Username: vinniefalco  
> Created at: 2018-05-14 02:15:36 UTC  
> Updated at: 2018-05-14 05:26:21 UTC  
> Url: https://github.com/boostorg/beast/issues/1126#issuecomment-388678057  

I'm seeing a pattern, so here is a tip which will help you with all of your programs:  
  
Always read the complete output of the compiler when an error happens, and study the text. You can almost always figure out what went wrong if you just read it carefully.

---

## Comment 10

> Username: ghost  
> Created at: 2018-05-14 02:26:24 UTC  
> Updated at: 2018-05-14 02:26:31 UTC  
> Url: https://github.com/boostorg/beast/issues/1126#issuecomment-388679436  

Thank you for the tip! The program compiles successfully now, but I'm getting a linking error. I'll try to figure this out on my own. I'll try to give an update tomorrow

---

## Comment 11

> Username: vinniefalco  
> Created at: 2018-05-14 03:15:47 UTC  
> Updated at: 2018-05-14 03:15:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1126#issuecomment-388685551  

Boost.System I'm guessing, or maybe OpenSSL

---

## Comment 12

> Username: ghost  
> Created at: 2018-05-14 14:56:43 UTC  
> Url: https://github.com/boostorg/beast/issues/1126#issuecomment-388847347  

Yeah it worked, I was incorrectly linking to Boost.System and OpenSSL. The program runs properly and I'm getting the expected response. Thank you!
