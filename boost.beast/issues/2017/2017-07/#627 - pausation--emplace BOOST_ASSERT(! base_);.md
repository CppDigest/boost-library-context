# #627 - pausation::emplace BOOST_ASSERT(! base_); [Closed]

> Username: monada99  
> Created at: 2017-07-09 09:20:52 UTC  
> Updated at: 2017-07-12 14:07:16 UTC  
> Closed at: 2017-07-12 14:07:16 UTC  
> Url: https://github.com/boostorg/beast/issues/627  

Hello, Can I call ws.async_read and ws.async_write methods from different threads? Because I have error:  
  
pausation.hpp:149  
void beast::websocket::detail::pausation::emplace(F&&) [with F = beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>&>::write_frame_op<beast::buffer_prefix_view<beast::consuming_buffers<boost::asio::const_buffers_1> >, beast::websocket::stream<boost::asio::basic_stream_socket<boost::asio::ip::tcp>&>::write_op<boost::asio::const_buffers_1, easynet::connection::send(const string&)::<lambda(const boost::system::error_code&)> > >]: Assertion `! base_' failed.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2017-07-09 11:21:33 UTC  
> Url: https://github.com/boostorg/beast/issues/627#issuecomment-313914008  

You cannot call *any* members from different threads concurrently. You have to make all your calls from the same implicit or explicit strand. Beast websocket streams work just like Asio streams - they are not thread-safe (even though you are *using* threads in your `io_service`).

---

## Comment 2

> Username: monada99  
> Created at: 2017-07-09 13:17:38 UTC  
> Url: https://github.com/boostorg/beast/issues/627#issuecomment-313919391  

Thank you, but it  does not help me, I use one thread for my 'async_read' and 'async_write' operations now but I have error still.  
  
pausation.hpp:149  
void beast::websocket::detail::pausation::emplace(F&&) [with F = beast::websocket::streamboost::asio::basic_stream_socket<boost::asio::ip::tcp&>::write_frame_opbeast::buffer_prefix_view<beast::consuming_buffers<boost::asio::const_buffers_1 >, beast::websocket::streamboost::asio::basic_stream_socket<boost::asio::ip::tcp&>::write_op<boost::asio::const_buffers_1, easynet::connection::send(const string&)::<lambda(const boost::system::error_code&)> > >]: Assertion `! base_' failed.  
  
What I can do? Thank you.

---

## Comment 3

> Username: vinniefalco  
> Created at: 2017-07-09 14:41:11 UTC  
> Url: https://github.com/boostorg/beast/issues/627#issuecomment-313923964  

The other reason that this assert comes up is if you try to call `async_read` or `async_write` twice without an intermediate completion. Is your code on GitHub where I can see it? Or can you post a gist?

---

## Comment 4

> Username: monada99  
> Created at: 2017-07-10 21:46:29 UTC  
> Url: https://github.com/boostorg/beast/issues/627#issuecomment-314259032  

Thank you very much, it help me. I call "async_write" several without an intermediate completion.

---

## Comment 5

> Username: vinniefalco  
> Created at: 2017-07-12 14:07:16 UTC  
> Url: https://github.com/boostorg/beast/issues/627#issuecomment-314780196  

Sounds like this is resolved, so I'll go ahead and close the issue. If you have another problem feel free to open a new issue any time, thanks!
