# #2257 - Compilation issues with gnu compiler version 11.1 [Closed]

> Username: daverio  
> Created at: 2021-06-11 09:30:34 UTC  
> Updated at: 2022-09-24 05:16:12 UTC  
> Closed at: 2022-09-24 05:16:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2257  

First: Thanks for Beast!  
  
So we just move to the gnu compiler 11.1. And beast does not compile. (everything is fine up to 10.3)  
  
The errors i get are:  
  
/usr/include/boost/beast/websocket/impl/stream_impl.hpp: In instantiation of ‘boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::impl_type::impl_type(Args&& ...) [with Args = {boost::asio::io_context&, boost::asio::ssl::context&}; NextLayer = boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >; bool deflateSupported = true]’:  
/usr/include/boost/smart_ptr/make_shared_object.hpp:256:5:   required from ‘typename boost::detail::sp_if_not_array<T>::type boost::make_shared(Args&& ...) [with T = boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> > >::impl_type; Args = {boost::asio::io_context&, boost::asio::ssl::context&}; typename boost::detail::sp_if_not_array<T>::type = boost::shared_ptr<boost::beast::websocket::stream<boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> > >::impl_type>]’  
/usr/include/boost/beast/websocket/impl/stream.hpp:54:42:   required from ‘boost::beast::websocket::stream< <template-parameter-1-1>, <anonymous> >::stream(Args&& ...) [with Args = {boost::asio::io_context&, boost::asio::ssl::context&}; NextLayer = boost::asio::ssl::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp> >; bool deflateSupported = true]’  
/home/daverio/Documents/silverboy/code/src/forwardsdata/book_keeper.cpp:40:81:   required from here  
/usr/include/boost/beast/websocket/impl/stream_impl.hpp:135:45: error: ‘static boost::beast::websocket::stream_base::duration boost::beast::websocket::stream_base::none()’ is inaccessible within this context  
  135 |         timeout_opt.handshake_timeout = none();  
      |                                         ~~~~^~  
In file included from /usr/include/boost/beast/websocket/stream.hpp:17,  
                 from /usr/include/boost/beast/websocket.hpp:18,  
                 from /home/daverio/Documents/silverboy/code/src/forwardsdata/book_keeper.hpp:9,  
                 from /home/daverio/Documents/silverboy/code/src/forwardsdata/book_keeper.cpp:1:  
/usr/include/boost/beast/websocket/stream_base.hpp:46:5: note: declared here  
   46 |     none() noexcept  
  
Basically I got similar error (inaccessibility of none() define in /usr/include/boost/beast/websocket/stream_base.hpp:46)  everywhere its used.  
  
Then also:  
/usr/include/boost/beast/core/impl/basic_stream.hpp:138:9: error: ‘static boost::beast::detail::stream_base::time_point boost::beast::detail::stream_base::never()’ is inaccessible within this context  
  
If someone have a clue how to solve it.. would be great!

---

## Comment 1

> Username: madmongo1  
> Created at: 2021-06-11 09:38:55 UTC  
> Url: https://github.com/boostorg/beast/issues/2257#issuecomment-859450839  

Please kindly share the entire command line that is being used to compile this code.  
  
Is there any way you can provide me with a minimal example on godbolt.org to demonstrate the issue?

---

## Comment 2

> Username: madmongo1  
> Created at: 2021-06-11 09:48:00 UTC  
> Url: https://github.com/boostorg/beast/issues/2257#issuecomment-859456869  

For example:  
https://godbolt.org/z/z8jT5zPdj

---

## Comment 3

> Username: stale[bot]  
> Created at: 2022-01-09 04:16:35 UTC  
> Url: https://github.com/boostorg/beast/issues/2257#issuecomment-1008226699  

This issue has been open for a while with no activity, has it been resolved?

---

## Comment 4

> Username: klemens-morgenstern  
> Created at: 2022-09-24 05:16:12 UTC  
> Url: https://github.com/boostorg/beast/issues/2257#issuecomment-1256863924  

@daverio feel free to reopen with the compiler invocation.
