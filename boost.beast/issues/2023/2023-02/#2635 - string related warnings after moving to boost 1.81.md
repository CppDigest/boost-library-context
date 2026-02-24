# #2635 - string related warnings after moving to boost 1.81 [Closed]

> Username: RavikumarTulugu  
> Created at: 2023-02-07 16:17:42 UTC  
> Updated at: 2023-02-08 06:06:54 UTC  
> Closed at: 2023-02-08 06:06:54 UTC  
> Url: https://github.com/boostorg/beast/issues/2635  

started seeing string related warnings after moving to boost 1.81 with clang++-15. please advise.   
  
In file included from /base/boost_1_81_0/boost/beast/core.hpp:46:  
In file included from /base/boost_1_81_0/boost/beast/core/static_string.hpp:14:  
/base/boost_1_81_0/boost/static_string/static_string.hpp:992:3: warning: definition of implicit copy assignment operator for 'basic_static_string<123, char>' is deprecated because it has a user-provided copy constructor [-Wdeprecated-copy-with-user-provided-copy]  
  basic_static_string(const basic_static_string& other) noexcept  
  ^  
81_0/boost/beast/websocket/rfc6455.hpp:168:8: note: in implicit copy assignment operator for 'boost::static_strings::basic_static_string<123, char>' first required here  
struct close_reason  
       ^  
/base/boost_1_81_0/boost/beast/websocket/detail/frame.hpp:205:12: note: in implicit move assignment operator for 'boost::beast::websocket::close_reason' first required here  
        cr = close_reason{};  
           ^  
/base/boost_1_81_0/boost/beast/websocket/impl/close.hpp:411:21: note: in instantiation of function template specialization 'boost::beast::websocket::detail::read_close<boost::beast::buffers_prefix_view<boost::beast::detail::buffers_pair<true>>>' requested here  
            detail::read_close(impl.cr, mb, ev);  
                    ^  
./ws.hh:300:9: note: in instantiation of member function 'boost::beast::websocket::stream<boost::beast::ssl_stream<boost::beast::basic_stream<boost::asio::ip::tcp>>, true>::close' requested here  
    ws_.close ( boost::beast::websocket::close_code::normal, ec );  
        ^

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2023-02-07 16:59:31 UTC  
> Url: https://github.com/boostorg/beast/issues/2635#issuecomment-1421106931  

This looks like it's fixed in static_string already , so the next release should not warn anymore   
https://github.com/boostorg/static_string/blob/develop/include/boost/static_string/static_string.hpp#L1180
