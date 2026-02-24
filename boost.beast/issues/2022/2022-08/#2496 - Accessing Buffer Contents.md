# #2496 - Accessing Buffer Contents [Closed]

> Username: efweber999  
> Created at: 2022-08-08 20:39:53 UTC  
> Updated at: 2022-08-11 14:56:14 UTC  
> Closed at: 2022-08-11 14:56:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2496  

Version of Beast: 330  
Compiler: gcc (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0  
  
Hi,  
  
I'm having difficulty accessing the buffer contents in the websockets-server-async example. Following the documentation for [Accessing Buffer Contents](https://www.boost.org/doc/libs/1_79_0/doc/html/boost_asio/reference/buffer.html#boost_asio.reference.buffer.accessing_buffer_contents), I added this line to libs/beast/example/websocket/server/async/websockets_server_async.cpp:  
  
```  
        // Echo the message  
        ws_.text(ws_.got_text());  
        // Added this line only.  
        unsigned char* p1 = static_cast<unsigned char*>(buffer_.data());  
        ws_.async_write(  
            buffer_.data(),  
            beast::bind_front_handler(  
                &session::on_write,  
                shared_from_this()));  
```  
  
Compiling produced the following error:  
  
```  
websockets_server_async.cpp: In member function ‘void session::on_read(boost::beast::error_code, std::size_t)’:  
websockets_server_async.cpp:134:71: error: invalid static_cast from type ‘boost::beast::basic_flat_buffer<std::allocator<char> >::mutable_buffers_type’ {aka ‘boost::asio::mutable_buffer’} to type ‘unsigned char*’  
  134 |         unsigned char* p1 = static_cast<unsigned char*>(buffer_.data());  
  
```  
I've tried various ideas to get past this, but I'm stuck. Help would be appreciated. Thanks.  
  
Here is some additional information. I used b2 to compile the examples.  
  
```  
./b2 -j8 -d+1 -d+2 --debug-building libs/beast/example cxxstd=11 \  
--build-dir=/home/beast \  
variant=release \  
2>&1 | tee /home/beast/beast_build.log  
```  
  
I then used the g++ compile settings directly from beast_build.log to re-compile websockets-server-async as I tried things.  
  
```  
# Compile  
g++ \  
-std=c++11 \  
-fvisibility-inlines-hidden \  
-fPIC \  
-m64 \  
-pthread \  
-O3 \  
-finline-functions \  
-Wno-inline \  
-Wall \  
-fvisibility=hidden \  
-DBOOST_ALL_NO_LIB=1 \  
-DBOOST_ASIO_DISABLE_BOOST_ARRAY=1 \  
-DBOOST_ASIO_DISABLE_BOOST_BIND=1 \  
-DBOOST_ASIO_DISABLE_BOOST_DATE_TIME=1 \  
-DBOOST_ASIO_DISABLE_BOOST_REGEX=1 \  
-DBOOST_ASIO_NO_DEPRECATED=1 \  
-DBOOST_ASIO_SEPARATE_COMPILATION \  
-DBOOST_BEAST_ALLOW_DEPRECATED \  
-DBOOST_BEAST_SEPARATE_COMPILATION \  
-DBOOST_COROUTINES_NO_DEPRECATION_WARNING=1 \  
-DNDEBUG \  
-D_GNU_SOURCE=1 \  
-D_XOPEN_SOURCE=600 \  
-I. \  
-I/usr/local/boost_1_79_0 \  
-I/usr/local/boost_1_79_0/libs/beast \  
-c \  
-o websocket_server_async.o \  
websocket_server_async.cpp  
  
# Link  
g++ \  
-o websocket-server-async \  
-Wl,--start-group \  
websocket_server_async.o \  
lib-asio.a \  
lib-beast.a \  
-Wl,-Bstatic \  
-Wl,-Bdynamic \  
-lrt \  
-Wl,--end-group \  
-fPIC \  
-m64 \  
-pthread \  
-std=c++11 \  
-fvisibility=hidden \  
-fvisibility-inlines-hidden  
```

---

## Comment 1

> Username: madmongo1  
> Created at: 2022-08-10 12:08:44 UTC  
> Url: https://github.com/boostorg/beast/issues/2496#issuecomment-1210583336  

You are not using the buffer sequence returned by the dynamic buffer's data() method correctly.  
  
Here is an example of how it works:  
  
```  
#include <boost/beast/core.hpp>  
#include <boost/asio.hpp>  
#include <iostream>  
  
  
int  
main()  
{  
    boost::beast::flat_buffer buffer_;  
  
    std::string src = "Hello, World!";  
  
    auto inbuf = boost::asio::buffer(src);  
    auto target = buffer_.prepare(inbuf.size());  
    boost::asio::buffer_copy(target, inbuf);  
    buffer_.commit(inbuf.size());  
  
    // this function returns the buffer sequence representing the output area of the   
    // dynamic buffer bufseq  
    auto bufseq = buffer_.data();  
  
    // for a flat_buffer, the buffer sequence us actually a single buffer  
    char* p1 = static_cast<char*>(bufseq.data());  
    std::size_t len = bufseq.size();  
  
    std::cout.write(p1, len);  
    std::cout.put('\n');  
  
}  
```  
  
expected output:  
```  
Hello, World!  
```  
  
https://godbolt.org/z/j4MYPaPn9

---

## Comment 2

> Username: efweber999  
> Created at: 2022-08-11 14:56:14 UTC  
> Url: https://github.com/boostorg/beast/issues/2496#issuecomment-1212104495  

Got it. Thank you.
