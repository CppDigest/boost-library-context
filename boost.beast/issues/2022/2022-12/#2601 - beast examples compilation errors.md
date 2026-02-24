# #2601 - beast examples compilation errors [Closed]

> Username: paulbkeelp  
> Created at: 2022-12-22 15:21:20 UTC  
> Updated at: 2022-12-22 17:52:20 UTC  
> Closed at: 2022-12-22 17:52:20 UTC  
> Url: https://github.com/boostorg/beast/issues/2601  

When I try to compile the individual cpp files with include/lib dir I get the below errors  
  
http_session.cpp:85:7: error: ‘message_generator’ in namespace ‘http’ does not name a type  
   85 | http::message_generator  
      |       ^~~~~~~~~~~~~~~~~  
http_session.cpp: In member function ‘void http_session::on_read(boost::beast::error_code, std::size_t)’:  
http_session.cpp:265:11: error: ‘message_generator’ is not a member of ‘http’  
  265 |     http::message_generator msg =  
      |           ^~~~~~~~~~~~~~~~~  
http_session.cpp:269:23: error: ‘msg’ was not declared in this scope  
  269 |     bool keep_alive = msg.keep_alive();  
      |                       ^~~  
http_session.cpp:274:12: error: ‘async_write’ is not a member of ‘beast’  
  274 |     beast::async_write(  
      |            ^~~~~~~~~~~  
http_session.cpp:274:12: note: suggested alternatives:  
In file included from /usr/include/boost/asio/write.hpp:1217,  
                 from /usr/include/boost/asio/buffered_write_stream.hpp:28,  
                 from /usr/include/boost/asio/buffered_stream.hpp:22,  
                 from /usr/include/boost/asio.hpp:43,  
                 from net.hpp:13,  
                 from http_session.hpp:13,  
                 from http_session.cpp:10:  
/usr/include/boost/asio/impl/write.hpp:961:1: note:   ‘boost::asio::async_write’  
  961 | async_write(AsyncWriteStream& s, DynamicBuffer_v2 buffers,  
      | ^~~~~~~~~~~  
In file included from /usr/include/boost/beast/http/write.hpp:745,  
                 from /usr/include/boost/beast/http.hpp:37,  
                 from /usr/include/boost/beast.hpp:16,  
                 from beast.hpp:13,  
                 from http_session.hpp:14,  
                 from http_session.cpp:10:  
/usr/include/boost/beast/http/impl/write.hpp:837:1: note:   ‘boost::beast::http::async_write’  
  837 | async_write(

---

## Comment 1

> Username: vinniefalco  
> Created at: 2022-12-22 16:16:52 UTC  
> Url: https://github.com/boostorg/beast/issues/2601#issuecomment-1363023522  

How did you install Boost? It looks like you are using a new example with an older version of Boost which does not contain the `message_generator` class (which was just newly added).

---

## Comment 2

> Username: paulbkeelp  
> Created at: 2022-12-22 17:51:48 UTC  
> Url: https://github.com/boostorg/beast/issues/2601#issuecomment-1363179810  

@vinniefalco thanks. I was doing the same.
