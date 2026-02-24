# #1674 - Possible "Use of memory after it is freed" in handler_ptr [Closed]

> Username: skannan89  
> Created at: 2019-08-06 00:45:17 UTC  
> Updated at: 2019-08-08 05:11:56 UTC  
> Closed at: 2019-08-08 05:11:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1674  

Love your library. Using it for many different things.   
  
I am using a modified version of http async ssl client example. `clang-analyzer` threw an error.  
This could either be a bug in my code or the beast library (v144). I saw that `handler_ptr` was deprecated in the latest version but couldn't find out the reason.    
```  
/usr/local/include/boost/beast/core/impl/handler_ptr.ipp:54:8: error: Use of memory after it is freed [clang-analyzer-cplusplus.NewDelete,-warnings-as-errors]  
    if(p_->t)  
       ^  
/build/build/../src/http-client-async-ssl.cpp:99:9: note: Assuming the condition is false  
    if (req_data.empty()) {  
        ^  
/build/build/../src/http-client-async-ssl.cpp:99:5: note: Taking false branch  
    if (req_data.empty()) {  
    ^  
/build/build/../src/http-client.cpp:108:5: note: Calling 'async_write<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> >, std::_Bind<void (http::session::*(std::shared_ptr<http::session>, std::_Placeholder<1>, std::_Placeholder<2>))(boost::system::error_code, unsigned long)>>'  
    http::async_write(  
    ^  
/usr/local/include/boost/beast/http/impl/write.ipp:786:5: note: Calling 'write_msg_op::operator()'  
    detail::write_msg_op<  
    ^  
/usr/local/include/boost/beast/http/impl/write.ipp:373:12: note: Calling 'async_write<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> >, boost::beast::http::detail::write_msg_op<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >, std::_Bind<void (http::session::*(std::shared_ptr<http::session>, std::_Placeholder<1>, std::_Placeholder<2>))(boost::system::error_code, unsigned long)>, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >>'  
    return async_write(d.s, d.sr, std::move(*this));  
           ^  
/usr/local/include/boost/beast/http/impl/write.ipp:718:46: note: Calling implicit destructor for 'write_op<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >, boost::beast::http::detail::write_msg_op<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >, std::_Bind<void (http::session::*(std::shared_ptr<http::session>, std::_Placeholder<1>, std::_Placeholder<2>))(boost::system::error_code, unsigned long)>, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >, boost::beast::http::detail::serializer_is_done, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> >>'  
            init.completion_handler, stream, sr}();  
                                             ^  
/usr/local/include/boost/beast/http/impl/write.ipp:718:46: note: Calling implicit destructor for 'write_msg_op<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >, std::_Bind<void (http::session::*(std::shared_ptr<http::session>, std::_Placeholder<1>, std::_Placeholder<2>))(boost::system::error_code, unsigned long)>, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> >>'  
/usr/local/include/boost/beast/http/impl/write.ipp:718:46: note: Calling '~handler_ptr'  
/usr/local/include/boost/beast/core/impl/handler_ptr.ipp:50:5: note: Taking false branch  
    if(! p_)  
    ^  
/usr/local/include/boost/beast/core/impl/handler_ptr.ipp:52:5: note: Taking false branch  
    if(--p_->n)  
    ^  
/usr/local/include/boost/beast/core/impl/handler_ptr.ipp:54:8: note: Assuming the condition is false  
    if(p_->t)  
       ^  
/usr/local/include/boost/beast/core/impl/handler_ptr.ipp:54:5: note: Taking false branch  
    if(p_->t)  
    ^  
/usr/local/include/boost/beast/core/impl/handler_ptr.ipp:65:5: note: Memory is released  
    delete p_;  
    ^  
/usr/local/include/boost/beast/http/impl/write.ipp:718:46: note: Returning from '~handler_ptr'  
            init.completion_handler, stream, sr}();  
                                             ^  
/usr/local/include/boost/beast/http/impl/write.ipp:718:46: note: Returning from destructor for 'write_msg_op<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >, std::_Bind<void (http::session::*(std::shared_ptr<http::session>, std::_Placeholder<1>, std::_Placeholder<2>))(boost::system::error_code, unsigned long)>, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> >>'  
/usr/local/include/boost/beast/http/impl/write.ipp:718:46: note: Returning from destructor for 'write_op<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >, boost::beast::http::detail::write_msg_op<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >, std::_Bind<void (http::session::*(std::shared_ptr<http::session>, std::_Placeholder<1>, std::_Placeholder<2>))(boost::system::error_code, unsigned long)>, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> > >, boost::beast::http::detail::serializer_is_done, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> >>'  
/usr/local/include/boost/beast/http/impl/write.ipp:373:12: note: Returning; memory was released  
    return async_write(d.s, d.sr, std::move(*this));  
           ^  
/usr/local/include/boost/beast/http/impl/write.ipp:786:5: note: Returning; memory was released  
    detail::write_msg_op<  
    ^  
/usr/local/include/boost/beast/http/impl/write.ipp:791:46: note: Calling implicit destructor for 'write_msg_op<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >, std::_Bind<void (http::session::*(std::shared_ptr<http::session>, std::_Placeholder<1>, std::_Placeholder<2>))(boost::system::error_code, unsigned long)>, true, boost::beast::http::basic_string_body<char, std::char_traits<char>, std::allocator<char> >, boost::beast::http::basic_fields<std::allocator<char> >>'  
            init.completion_handler, stream, msg}();  
                                             ^  
/usr/local/include/boost/beast/http/impl/write.ipp:791:46: note: Calling '~handler_ptr'  
/usr/local/include/boost/beast/core/impl/handler_ptr.ipp:50:5: note: Taking false branch  
    if(! p_)  
    ^  
/usr/local/include/boost/beast/core/impl/handler_ptr.ipp:52:5: note: Taking false branch  
    if(--p_->n)  
    ^  
/usr/local/include/boost/beast/core/impl/handler_ptr.ipp:54:8: note: Use of memory after it is freed  
    if(p_->t)  
       ^  
```  
### Version of Beast  
  
`#define BOOST_BEAST_VERSION 144`  
  
### Steps necessary to reproduce the problem  
  
Running clang analyzer on modified http async ssl client example.  
  
### All relevant compiler information  
  
Ubuntu 18.10 -> clang analyzer tool

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-08-06 02:12:25 UTC  
> Url: https://github.com/boostorg/beast/issues/1674#issuecomment-518468563  

`handler_ptr` is deprecated because `async_base` is safer, more flexible, easier to use and generally better for achieving the same goal:  
https://www.boost.org/doc/libs/1_70_0/libs/beast/doc/html/beast/ref/boost__beast__async_base.html  
  
There is nothing "wrong" with `handler_ptr`, it just isn't as good. I am very confident that there are no bugs in `handler_ptr`, so either you have a false positive or your code is somehow breaking preconditions.

---

## Comment 2

> Username: skannan89  
> Created at: 2019-08-08 05:11:56 UTC  
> Url: https://github.com/boostorg/beast/issues/1674#issuecomment-519366612  

Thanks for your response. It seems to be a false positive in clang analyzer. I disabled that check.
